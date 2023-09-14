<template>
  <AppHeader />
  <div class="flex flex-row">
    <Sidebar @category-changed="updateBookmarkList" />
    <app-bookmark-list v-if="bookmarkList.length > 0" :items="bookmarkList" />
    <div v-else>Bookmark bulunmamaktadır..</div>
  </div>
</template>
<script setup>
import Sidebar from "@/components/Home/SideBar";
import { ref, onMounted, inject } from "vue";
 import { useStore } from "vuex";
const appAxios = inject("appAxios");
const socket = inject("socket");
const bookmarkList = ref([]);
 const store = useStore();

onMounted(() => {
  socket.on("NEW_BOOKMARK_ADDED", bookmark => {
    bookmarkList.value.push(bookmark);
  });
});

const fetchData = () => {
  appAxios.get("/Bookmark").then(bookmark_list_response => { //https://localhost:7255/api/Bookmark

    bookmarkList.value = bookmark_list_response?.data || []; 
  });
   }
   //! Bookmark olarak eklediklerimizi çekmek için user_bookmarks üzerinden çekelim..
appAxios.get('/UserBookmark').then((user_bookmark_response) => {
  console.log('user_bookmark_response :>> ', user_bookmark_response);
  store.commit('setBookmarks', user_bookmark_response?.data);
});

//   //! Like olarak eklediklerimizi çekmek için user_likes üzerinden çekelim..
appAxios.get('/UserLike').then((user_likes_response) => {
  console.log('user_likes_response :>> ', user_likes_response);
  store.commit('setLikes', user_likes_response?.data);
});

  const updateBookmarkList = (categoryId) => {
  const url = categoryId ? `/Bookmark/GetBookmarkByCategory?categoryId=${categoryId}` : `/Bookmark`;
  appAxios.get(url).then((bookmark_list_response) => {
    bookmarkList.value = bookmark_list_response?.data || [];
    console.log('BookmarkList.value :>> ', bookmarkList.value);
  });
};

//   const updateBookmarkList = (categoryId) => {
  
//      appAxios.get(url).then(bookmark_list_response => {
//     bookmarkList.value = bookmark_list_response?.data || [];
//    });
// };
fetchData();

</script>