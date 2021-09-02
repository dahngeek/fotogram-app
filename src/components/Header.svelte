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



const dataUrl = `/api/v1/auth/me`;

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
    <!-- This example requires Tailwind CSS v2.0+ -->
<div class="relative bg-white">
    <div class="max-w-7xl mx-auto px-4 sm:px-6">
      <div class="flex justify-between items-center border-b-2 border-gray-100 py-6 md:justify-start md:space-x-10">
        <div class="flex justify-start lg:w-0 lg:flex-1">
          <a href="/">
            <span class="sr-only">Nutrigram</span>
            <img class="h-8 w-auto sm:h-10" src="https://tailwindui.com/img/logos/workflow-mark-indigo-600.svg" alt="">
          </a>
        </div>
        <div class="-mr-2 -my-2 md:hidden">
          <a href="/subir" class="whitespace-nowrap inline-flex items-center justify-center px-4 py-2 border border-transparent rounded-md shadow-sm text-base font-medium text-white bg-indigo-600 hover:bg-indigo-700">
            Subir una Foto
          </a>
        </div>
        <nav class="md:flex space-x-10">
  
          <a href="/" class="text-base font-medium text-gray-500 hover:text-gray-900">
            Inicio
          </a>
          <!-- <a href="/usuarios" class="text-base font-medium text-gray-500 hover:text-gray-900">
            Usuarios
          </a> -->
          <a href="/me" class="text-base font-medium text-gray-500 hover:text-gray-900">
            Mi Perfil
          </a>
        </nav>
        <div class="hidden md:flex items-center justify-end md:flex-1 lg:w-0">
          <a href="/subir" class="whitespace-nowrap inline-flex items-center justify-center px-4 py-2 border border-transparent rounded-md shadow-sm text-base font-medium text-white bg-indigo-600 hover:bg-indigo-700">
            Subir una Foto
          </a>
          <a href="/me" class="ml-8 whitespace-nowrap text-base font-medium text-gray-500 hover:text-gray-900">
            <div class="flex">
                <div class="rounded-full h-8 w-8 bg-gray-500 flex items-center justify-center overflow-hidden">
                    <img src="{avatar.substring(0, avatar.indexOf("?"))}" alt="profilepic">
                </div>
                <span class="pt-1 ml-2 font-bold text-sm">{username}</span>
            </div>
          </a>
        </div>
      </div>
    </div>
  
    <!--
      Mobile menu, show/hide based on mobile menu state.
  
      Entering: "duration-200 ease-out"
        From: "opacity-0 scale-95"
        To: "opacity-100 scale-100"
      Leaving: "duration-100 ease-in"
        From: "opacity-100 scale-100"
        To: "opacity-0 scale-95"
    -->
  </div>
  
</main>