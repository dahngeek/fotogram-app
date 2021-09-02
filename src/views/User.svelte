<script>
    import router from 'page'
	import { onMount } from "svelte";
    import { persist, localStorage } from "@macfja/svelte-persistent-store"
    import { writable } from "svelte/store"
    import moment from 'moment';

    let authToken = persist(writable('token'), localStorage(), 'token')
    // sconsole.log($authToken);
    const myHeaders = new Headers();

    myHeaders.append('Content-Type', 'application/json');
    myHeaders.append('Authorization', 'Bearer ' + $authToken);

    export let params;

    let avatar = '';
    let fullname = 'Nombre';
    let username = '-----';
    let email = '-----@-----.---';

    var images = [];
    const getUsername = () => {
        const dataUrl = `/api/v1/users/`+params.user_name;

        fetch(dataUrl, {
            method: "GET",
            headers: myHeaders,
        }).then((response) => {
            if (response.ok) return response.json();
            return response.json().then(response => {throw new Error(response.message)})
        }).then(function(response){
            if(response.success){
                console.log(response);
                avatar = response.data.avatar;
                fullname = response.data.fullname;
                username = response.data.username;
                images = response.data.images;
            }
        }).catch((err) => {
            errormessage = err
        });
    };

    getUsername();
	
</script>

<main>
    <div class="w-full bg-gray-50 flex justify-center items-center my-5">
        <div class="h-56 w-72 absolute flex justify-center items-center">
          <img
            class="object-cover h-20 w-20 rounded-full"
            src="{avatar.substring(0, avatar.indexOf("?"))}"
            alt=""
          />
        </div>
  
        <div
          class="
            h-56
            mx-4
            w-5/6
            bg-blue-400
            rounded-3xl
            shadow-md
            sm:w-80 sm:mx-0
          "
        >
          <div class="h-1/2 w-full flex justify-between items-baseline px-3 py-5">
          </div>
          <div
            class="
              bg-white
              h-1/2
              w-full
              rounded-3xl
              flex flex-col
              justify-around
              items-center
            "
          >
            <div class="w-full h-1/2 flex justify-between items-center px-3 pt-2">
            </div>
            <div class="w-full h-1/2 flex flex-col justify-center items-center">
              <h1 class="text-gray-700 font-bold">{fullname}</h1>
              <h1 class="text-gray-500 text-sm">{username}</h1>
            </div>
          </div>
        </div>
      </div>
    <div class="bg-gray-100 min-h-screen py-32 px-10 ">
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 md:gap-x-10 xl-grid-cols-4 gap-y-10 gap-x-6 "> 
      
        {#each images as { tags, files, caption, user, createdAt }, i}
            <div class="container mx-auto shadow-lg rounded-lg max-w-md hover:shadow-2xl transition duration-300">
                <img src="{files[0].substring(0, files[0].indexOf("?"))}" alt="" class="rounded-t-lg w-full">
                <div class="p-6">
                <h1 class="md:text-1xl text-xl hover:text-indigo-600 transition duration-200  font-bold text-gray-900 ">{caption}</h1>
                <p class="text-gray-700 my-2 hover-text-900 ">{tags.join(', ')}</p>
                <p class="text-gray-500 my-2 hover-text-900 ">{moment(createdAt).locale('es').calendar()}</p>
                </div>
            </div>
	    {/each}
      
          </div>
      </div>
</main>