<script>
  import router from 'page'
  import { persist, localStorage } from "@macfja/svelte-persistent-store"
  import { writable } from "svelte/store"

  let authToken = persist(writable('token'), localStorage(), 'token')

  var errormessage = ''

  var datos = {
  };



  function handleClick(event) {
  event.preventDefault()
      var formAEnviar = JSON.stringify(datos);
      fetch("http://localhost:5000/api/v1/auth/login", {
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
                  router.redirect('/');
              }
          }).catch((err) => {
              errormessage = err
          });
}
</script>

<main>
    <div class="flex items-center justify-center bg-gray-50 mt-20 py-3 px-4 sm:px-6 lg:px-8">
        <div class="max-w-md w-full space-y-2">
          <div>
            <img class="mx-auto h-12 w-auto" src="https://tailwindui.com/img/logos/workflow-mark-indigo-600.svg" alt="Workflow">
            <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
              Inicia Sesión en tu Cuenta
            </h2>
            <p class="mt-2 text-center text-sm text-gray-600">
              Or
              <a href="/registro" class="font-medium text-indigo-600 hover:text-indigo-500">
                Registrate haciendo click aquí
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
                <label for="email-address" class="sr-only">Email address</label>
                <input bind:value={datos.email} id="email-address" name="email" type="email" autocomplete="email" required class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm" placeholder="Email address">
              </div>
              <div>
                <label for="password" class="sr-only">Password</label>
                <input bind:value={datos.password} id="password" name="password" type="password" autocomplete="current-password" required class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm" placeholder="Password">
              </div>
            </div>
      
            <div>
              <button on:click={handleClick} type="submit" class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                <span class="absolute left-0 inset-y-0 flex items-center pl-3">
                  <!-- Heroicon name: solid/lock-closed -->
                  <svg class="h-5 w-5 text-indigo-500 group-hover:text-indigo-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                    <path fill-rule="evenodd" d="M5 9V7a5 5 0 0110 0v2a2 2 0 012 2v5a2 2 0 01-2 2H5a2 2 0 01-2-2v-5a2 2 0 012-2zm8-2v2H7V7a3 3 0 016 0z" clip-rule="evenodd" />
                  </svg>
                </span>
                Sign in
              </button>
            </div>
          </form>
        </div>
      </div>
</main>