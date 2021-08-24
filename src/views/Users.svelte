<script>
    import router from 'page'

    import { persist, localStorage } from "@macfja/svelte-persistent-store"
    import { writable } from "svelte/store"

    let authToken = persist(writable('token'), localStorage(), 'token')
    console.log($authToken);
    const myHeaders = new Headers();

    myHeaders.append('Content-Type', 'application/json');
    myHeaders.append('Authorization', 'Bearer ' + $authToken);

    var users = [];
    var loading = false;

	const dataUrl = `http://localhost/api/v1/users`;

    function getPictures(serviceurl) {
        loading = true;
        fetch(serviceurl, {
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
                loading = false;
                users = response.data;
            }
        }).catch((err) => {
            console.log(err);
        });
    }

    getPictures(dataUrl);

</script>

<main>
    <div class="container mx-auto">

        <div class="bg-gray-100 w-1/2 rounded px-6 mx-auto">
            <div class="border-l-4 border-red-400 -ml-6 pl-6 flex items-center justify-between my-4">
                <div class="font-semibold text-gray-800">Alumnos</div>
            </div>
            <hr class="-mx-6"/>
            {#each users as { imageCount, fullname, username, avatar, createdAt }, i}
                <div class="flex items-center justify-between my-4">
                    <div class="w-16">
                    <a href="/user/{username}">
                        <img class="w-12 h-12 rounded-full" src="{avatar}">
                    </a>
                    </div>
                    <div class="flex-1 pl-2">
                        <div class="text-gray-700 font-semibold">
                            <a href="/user/{username}">{fullname}</a>
                        </div>
                        <div class="text-gray-600 font-thin">
                        {username}
                        </div>
                    </div>
                    <div class="text-red-400">
                        <div class="text-gray-700 font-semibold">
                            <a href="/user/{username}">{imageCount} Posts</a>
                        </div>
                    </div>
                </div>
                <hr class="boder-b-0 my-4"/>
            {/each}
        </div>
        {#if loading}
        <div class="flex items-center justify-center ">
            <div class="w-16 h-16 border-b-2 border-gray-900 rounded-full animate-spin"></div>
        </div>
        {/if}
    </div>
</main>