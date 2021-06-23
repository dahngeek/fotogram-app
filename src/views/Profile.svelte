<script>
    import router from 'page'
	import { onMount } from "svelte";
    import { persist, localStorage } from "@macfja/svelte-persistent-store"
    import { writable } from "svelte/store"
    let authToken = persist(writable('token'), localStorage(), 'token')
    console.log($authToken);
    const myHeaders = new Headers();

    myHeaders.append('Content-Type', 'application/json');
    myHeaders.append('Authorization', 'Bearer ' + $authToken);

    let avatar = '';
    let fullname = 'Nombre';
    let username = '-----';
    let email = '-----@-----.---';



	const dataUrl = `http://localhost:5000/api/v1/auth/me`;

    fetch(dataUrl, {
        method: "GET",
        headers: myHeaders,
        }).then((response) => {
            if (response.status == 403) {
                router.redirect('/login');
            }
            if (response.ok) return response.json();
            return response.json().then(response => {throw new Error(response.message)})
        }).then(function(response){
            if(response.success){
                console.log(response);
                avatar = response.data.avatar;
                fullname = response.data.fullname;
                username = response.data.username;
                email = response.data.email;
            }
        }).catch((err) => {
            console.log(err);
        });
	
</script>

<main>
    <div class="container mx-auto">
        <div class="flex items-center h-screen w-full justify-center">

            <div class="max-w-xs">
                <div class="bg-white shadow-xl rounded-lg py-3">
                    <div class="photo-wrapper p-2">
                        <img class="w-32 h-32 rounded-full mx-auto" src="{avatar}" alt="[as]">
                    </div>
                    <div class="p-2">
                        <h3 class="text-center text-xl text-gray-900 font-medium leading-8">{fullname}</h3>
                        <div class="text-center text-gray-400 text-xs font-semibold">
                            <p>---------</p>
                        </div>
                        <table class="text-xs my-3">
                            <tbody><tr>
                                <td class="px-2 py-2 text-gray-500 font-semibold">Usuario</td>
                                <td class="px-2 py-2">{username}</td>
                            </tr>
                            <tr>
                                <td class="px-2 py-2 text-gray-500 font-semibold">Email</td>
                                <td class="px-2 py-2">{email}</td>
                            </tr>
                        </tbody></table>
            
                    </div>
                </div>
            </div>
            
            </div> 
    </div>
</main>