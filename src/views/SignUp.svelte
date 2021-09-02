<script>
    import router from 'page'
    import { persist, localStorage } from "@macfja/svelte-persistent-store"
    import { writable } from "svelte/store"
    import ImageTools from '../ImageTools'

    let authToken = persist(writable('token'), localStorage(), 'token')


    let files;
    var changed = false;
    var filename = '';
    var errormessage = ''

    var datos = {
    };

    $: if (files) {
        // Note that `files` is of type `FileList`, not an Array:
        // https://developer.mozilla.org/en-US/docs/Web/API/FileList
        console.log(files);
        filename = files[0].name;

        ImageTools.resize(files[0], {
        width: 800, // maximum width
        height: 800 // maximum height
        }, function(blob, didItResize) {
            // didItResize will be true if it managed to resize it, otherwise false (and will return the original file as 'blob')
            // document.getElementById('preview').src = window.URL.createObjectURL(blob);
            // you can also now upload this blob using an XHR.
            const filesend = new File([blob], filename, {
                        type: 'image/jpeg',
                        lastModified: Date.now()
                    });
            let data = new FormData();
            data.append('file1', filesend);
            fetch('/api/v1/principal/upload', {
                method: 'POST',
                credentials: 'same-origin',
                body: data
            }).then(function(res){
                return res.json();
            }).then(function(response){
                if(response.success){
                    changed = true;
                    datos.avatar = response.data.url
                }
            });
        });
    }

    function handleClick(event) {
		event.preventDefault()
        var formAEnviar = JSON.stringify(datos);
        fetch("/api/v1/auth/signup", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                "Accept": "application/json"
            },
            body: formAEnviar
            }).then((response) => {
                if (response.ok) return response.json();
                return response.json().then(response => {throw new Error(response.message)})
            }).then(function(response){
                if(response.success){
                    $authToken = response.token
                    router.redirect('/me');
                }
            }).catch((err) => {
                errormessage = err
            });
	}
</script>

<main>
    <div class="flex items-center justify-center bg-gray-50 mt-10 py-3 px-4 sm:px-6 lg:px-8">
        <div class="max-w-md w-full space-y-2">
            <div>
                <img class="mx-auto h-12 w-auto" src="https://tailwindui.com/img/logos/workflow-mark-indigo-600.svg" alt="Workflow">
                <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
                    Registro
                </h2>
                <p class="mt-2 text-center text-sm text-gray-600">
                    ¿Ya tienes cuenta?
                    <a href="/login" class="font-medium text-indigo-600 hover:text-indigo-500">
                        Inicia sesión
                    </a>
                </p>
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
            <form class="mt-8 space-y-6" action="#" method="POST">
                <input type="hidden" name="remember" value="true">
                <div class="rounded-md shadow-sm -space-y-px">
                    <div>
                        <label for="fullname" class="sr-only">Nombre Completo</label>
                        <input bind:value={datos.fullname} id="fullname" name="fullname" type="text" autocomplete="fullname" required class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm" placeholder="Nombre Completo">
                    </div>
                    <div>
                        <label for="email-address" class="sr-only">Correo Electrónico</label>
                        <input bind:value={datos.email} id="email-address" name="email" type="email" autocomplete="email" required class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm" placeholder="Correo Electrónico">
                    </div>
                    <div>
                        <label for="username" class="sr-only">Usuario</label>
                        <input bind:value={datos.username} id="username" name="username" type="text" autocomplete="username" required class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm" placeholder="Nombre de Usuario (@usuario)">
                    </div>
                    <div>
                        <label for="password" class="sr-only">Contraseña</label>
                        <input bind:value={datos.password} id="password" name="password" type="password" autocomplete="current-password" required class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm" placeholder="Contraseña">
                    </div>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700">
                      Foto de Perfil
                    </label>
                    <div class="mt-1 flex items-center">
                      {#if changed}
                        <div class="rounded-full" style="width: 50px;height:50px;background-image: url({datos.avatar});background-size:cover;"></div>
                      {:else}
                        <span class="inline-block h-12 w-12 rounded-full overflow-hidden bg-blue-300">
                            <svg class="h-full w-full text-gray-300" fill="currentColor" viewBox="0 0 24 24">
                            <path d="M24 20.993V24H0v-2.996A14.977 14.977 0 0112.004 15c4.904 0 9.26 2.354 11.996 5.993zM16.002 8.999a4 4 0 11-8 0 4 4 0 018 0z" />
                            </svg>
                        </span>
                      {/if}
                      <label for="file1" class="ml-5 cursor-pointer bg-white py-2 px-3 border border-gray-300 rounded-md shadow-sm text-sm leading-4 font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                        {#if changed}
                        <span>{filename}</span>
                        {:else}
                            <span>Upload a file</span>
                            <input id="file1" name="file1" type="file" class="sr-only" accept="image/png, image/jpeg" bind:files>
                        {/if}
                      </label>
                    </div>
                  </div>
                <div>
                    <button type="submit" on:click={handleClick} class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                        <span class="absolute left-0 inset-y-0 flex items-center pl-3">
                            <!-- Heroicon name: solid/lock-closed -->
                            <svg class="h-5 w-5 text-indigo-500 group-hover:text-indigo-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                <path fill-rule="evenodd" d="M5 9V7a5 5 0 0110 0v2a2 2 0 012 2v5a2 2 0 01-2 2H5a2 2 0 01-2-2v-5a2 2 0 012-2zm8-2v2H7V7a3 3 0 016 0z" clip-rule="evenodd" />
                            </svg>
                        </span>
                        Registrarme :)
                    </button>
                </div>
            </form>
        </div>
    </div>
</main>