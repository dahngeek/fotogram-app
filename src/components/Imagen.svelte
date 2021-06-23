<script>
  export let user;
  export let caption;
  export let files;
  export let tags;
</script>
<main>
    <div class="mx-auto my-10 rounded-xl p-8 overflow-hidden border w-full lg:w-6/12 md:w-6/12 bg-white">
        <div class="w-full flex justify-between p-3">
          <div class="flex">
            <div class="rounded-full h-8 w-8 bg-gray-500 flex items-center justify-center overflow-hidden">
              <img src="{user.avatar}" alt="profilepic">
            </div>
            <span class="pt-1 ml-2 font-bold text-sm">{user.username}</span>
          </div>
          <span class="px-2 hover:bg-gray-300 cursor-pointer rounded"><i class="fas fa-ellipsis-h pt-2 text-lg"></i></span>
        </div>
        <img class="w-full bg-cover" src="{files[0]}">
        <div class="px-3 pb-2">
          <div class="pt-2">
            <i class="far fa-heart cursor-pointer"></i>
            <!-- <span class="text-sm text-gray-400 font-medium">12 likes</span> -->
          </div>
          <div class="pt-1">
            <div class="mb-2 text-md">
              {caption}
            </div>
          </div>
          <!-- <div class="text-sm mb-2 text-gray-400 cursor-pointer font-medium">View all 14 comments</div>
          <div class="mb-2">
            <div class="mb-2 text-sm">
              <span class="font-medium mr-2">razzle_dazzle</span> Dude! How cool! I went to New Zealand last summer and had a blast taking the tour! So much to see! Make sure you bring a good camera when you go!
            </div>
          </div> -->
        </div>
      </div>
</main>