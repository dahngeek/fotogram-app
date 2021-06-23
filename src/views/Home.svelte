<script>
    import Imagen from '../components/Imagen.svelte'
    import router from 'page'

    import { persist, localStorage } from "@macfja/svelte-persistent-store"
    import { writable } from "svelte/store"

    let authToken = persist(writable('token'), localStorage(), 'token')
    console.log($authToken);
    const myHeaders = new Headers();

    myHeaders.append('Content-Type', 'application/json');
    myHeaders.append('Authorization', 'Bearer ' + $authToken);

    var images = [];

	const dataUrl = `http://localhost:5000/api/v1/principal`;

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
                images = response.data;
            }
        }).catch((err) => {
            console.log(err);
        });
</script>

<main>
    <div class="container mx-auto">
        {#each images as { tags, files, caption, user }, i}
		<Imagen tags={tags} files={files} caption={caption} user={user}/>
	    {/each}
    </div>
</main>