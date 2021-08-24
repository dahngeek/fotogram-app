<script>
    import router from 'page'
    import { persist, localStorage } from "@macfja/svelte-persistent-store"
    import { writable } from "svelte/store"
import Header from '../components/Header.svelte';

    let authToken = persist(writable('token'), localStorage(), 'token')
    var errormessage = '';
    let files;
    var filename = '';
    var publicando = false;
    var uploading = false;
    var uploaded = false;
    var datos = {
        files:['https://img.freepik.com/free-vector/image-upload-concept-landing-page_52683-27130.jpg?size=338&ext=jpg']
    };

    $: if (files) {
        // Note that `files` is of type `FileList`, not an Array:
        // https://developer.mozilla.org/en-US/docs/Web/API/FileList
        console.log(files);
        filename = files[0].name;

        let data = new FormData();
        data.append('file1', files[0]);
        uploading = true;
        if(!uploaded){
            fetch('/api/v1/principal/upload', {
                method: 'POST',
                credentials: 'same-origin',
                body: data
            }).then(function(res){
                if (res.status == 403) {
                    router.redirect('/login');
                }
                return res.json();
            }).then(function(response){
                if(response.success){
                    uploaded = true;
                    datos.files = [response.data.url]
                }
            });
        }
    }

    const myHeaders = new Headers();

    myHeaders.append('Content-Type', 'application/json');
    myHeaders.append('Authorization', 'Bearer ' + $authToken);

    function handleClick(event) {
		event.preventDefault()
        datos.tags = datos.tags.split(',');
        var formAEnviar = JSON.stringify(datos);
        publicando = true;
        fetch("/api/v1/principal/", {
            method: "POST",
            headers: myHeaders,
            body: formAEnviar
            }).then((response) => {
                if (response.status == 403) {
                    router.redirect('/login');
                }
                if (response.ok) return response.json();
                return response.json().then(response => {throw new Error(response.message)})
            }).then(function(response){
                console.log(response);
                if(response.success){
                    router.redirect('/');
                }
            }).catch((err) => {
                errormessage = err
            });
	}

</script>

<div class="relative min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8 bg-gray-500 bg-no-repeat bg-cover relative items-center"
	style="background-image: url(https://images.unsplash.com/photo-1621243804936-775306a8f2e3?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=1950&q=80);">
	<div class="absolute bg-black opacity-60 inset-0 z-0"></div>
	<div class="sm:max-w-lg w-full p-10 bg-white rounded-xl z-10">
		<div class="text-center">
			<h2 class="mt-5 text-3xl font-bold text-gray-900">
				Publicar una Imagen
			</h2>
		</div>
        {#if errormessage}
        <div class="p-2">
            <div class="inline-flex items-center bg-white leading-none text-pink-600 rounded-full p-2 shadow text-teal text-sm">
                <span class="inline-flex bg-pink-600 text-white rounded-full h-6 px-3 justify-center items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                </span>
                <span class="inline-flex px-2">{errormessage}</span>
            </div>
            </div>
        {/if}
        {#if !publicando}
        <form class="mt-8 space-y-3" action="#" method="POST">
                    <div class="grid grid-cols-1 space-y-2">
                        <label class="text-sm font-bold text-gray-500 tracking-wide">Titulo</label>
                            <input bind:value={datos.caption} class="text-base p-2 border border-gray-300 rounded-lg focus:outline-none focus:border-indigo-500" type="text" placeholder="Ej. Mi almuerzo de hoy...">
                    </div>
                    <div class="grid grid-cols-1 space-y-2">
                                    <label class="text-sm font-bold text-gray-500 tracking-wide">Imagen</label>
                        <div class="flex items-center justify-center w-full">
                                <label class="flex flex-col rounded-lg border-4 border-dashed w-full h-60 p-10 group text-center">
                                    <div class="h-full w-full text-center flex flex-col items-center justify-center items-center  ">
                                        <!---<svg xmlns="http://www.w3.org/2000/svg" class="w-10 h-10 text-blue-400 group-hover:text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
                                        </svg>-->
                                        <div class="flex flex-auto max-h-48 w-2/5 mx-auto -mt-10">
                                        <img class="has-mask h-36 object-center" src="{datos.files[0]}" alt="freepik">
                                        </div>
                                        {#if uploading}
                                            {#if uploaded}
                                                <p class="pointer-none text-gray-500 ">¡Listo para publicar!</p>
                                            {:else}
                                            <p class="pointer-none text-gray-500 ">Cargando...</p>
                                            {/if}
                                        {:else}
                                            <p class="pointer-none text-gray-500 ">Aquí puedes <span class="text-blue-600 hover:underline">seleccionar una foto</span> de tu computadora, para publicar.</p>
                                        {/if}
                                    </div>
                                    {#if !uploaded}
                                        <input type="file" class="hidden" accept="image/png, image/jpeg" bind:files>
                                    {/if}
                                </label>
                        </div>
                    </div>
                    <div class="grid grid-cols-1 space-y-2">
                        <label class="text-sm font-bold text-gray-500 tracking-wide">Menú:</label>
                            <input bind:value={datos.tags} class="text-base p-2 border border-gray-300 rounded-lg focus:outline-none focus:border-indigo-500" type="text" placeholder="Ej. Empanadas, Ensalada, etc.">
                    </div>
                    <div>
                        <button type="submit" on:click={handleClick} class="my-5 w-full flex justify-center bg-blue-500 text-gray-100 p-4  rounded-full tracking-wide
                                    font-semibold  focus:outline-none focus:shadow-outline hover:bg-blue-600 shadow-lg cursor-pointer transition ease-in duration-300">
                        Publicar
                    </button>
                    </div>
        </form>
        {:else}
            <div class="flex items-center justify-center ">
                <div class="w-16 h-16 border-b-2 border-gray-900 rounded-full animate-spin"></div>
            </div>
        {/if}
	</div>
</div>

<style>
	.has-mask {
		position: absolute;
		clip: rect(10px, 150px, 130px, 10px);
	}
</style>