<template>
  <button @click="cb('/helloWorld1')">click</button>
  <button @click="cb('/helloWorld2')">click2</button>
</template>
<script setup>
let wr = function (type) {
  let orig = window.history[type];
  return function () {
    let rv = orig.apply(this, arguments);
    let e = new Event(type);
    e.arguments = arguments;
    window.dispatchEvent(e);
    return rv;
  };
};
window.history.pushState = wr("pushState");

const cb = (url) => {
  const state = { page_id: 1, user_id: 5, url };
  const title = url;
  history.pushState(state, title, url);
};

// 路由
const routes = [
  { path: "/", component: "App" },
  { path: "/helloWorld1", component: "<p>我是一个 helloWorld1</p>" },
  { path: "/helloWorld2", component: "<p>我是一个 helloWorld2</p>" },
];
window.addEventListener("pushState", (e) => {
  let url = e.arguments[2];

  let com = routes.find((ele) => ele.path === url)["component"];
  document.getElementById("app").innerHTML = com;
});
// 不能原生的监听 pushState
// 浏览器的行为才触发   go back replace
window.onpopstate = function (event) {
  let url = (event.state && event.state.url) || "/";

  let com = routes.find((ele) => ele.path === url)["component"];
  document.getElementById("app").innerHTML = com;
};
</script>

<style scoped>
button {
  border: 1px solid red;
}
</style>
