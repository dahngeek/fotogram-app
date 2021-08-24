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
    var pagina = 0;
    var showMore = false;
    var showPrev = false;
    var loading = false;
    var prevURL = '';
    var moreURL = '';

	const dataUrl = `http://localhost/api/v1/principal`;

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
                images = response.data;

                function hasMorePages(link) {
                    return link.rel === 'next';
                }
                function hasLessPages(link) {
                    return link.rel === 'prev';
                }

                if(response.links){
                    var moreLink = response.links.find(hasMorePages);
                    if (moreLink) {
                        showMore = true;
                        moreURL = moreLink.href;
                    } else {
                        showMore = false;
                    }

                    var prevLink = response.links.find(hasLessPages);
                    if (prevLink) {
                        showPrev = true;
                        prevURL = prevLink.href;
                    } else {
                        showPrev = false;
                    }
                }

            }
        }).catch((err) => {
            console.log(err);
        });
    }

    getPictures(dataUrl);

    function prevPage(event) {
        event.preventDefault();
        getPictures(prevURL);
    }

    function nextPage(event) {
        event.preventDefault();
        getPictures(moreURL);
    }
</script>

<main>
    <div class="container mx-auto">

        {#each images as { tags, files, caption, user, createdAt }, i}
		    <Imagen tags={tags} files={files} caption={caption} user={user} createdAt={createdAt}/>
	    {/each}
        {#if loading}
        <div class="flex items-center mt-6 justify-center ">
            <div class="w-16 h-16 border-b-2 border-gray-900 rounded-full animate-spin"></div>
        </div>
        {/if}
        {#if showPrev}
            <button on:click={prevPage} type="button" class="group mb-5 relative w-50 flex mx-auto justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                Anterior
            </button>
        {/if}
        {#if showMore}
            <button on:click={nextPage} type="button" class="group mb-5 relative w-50 flex mx-auto justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                Siguiente
            </button>
        {/if}
    </div>
</main>