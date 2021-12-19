<template>
  <router-view />
</template>

<style lang="scss">
@import url("https://use.fontawesome.com/releases/v5.15.2/css/all.css");
@import url("https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
$whiteBlue: #e7ecef;
$darkBlue: #274c77;
$normalBlue: #6aa7cf;
$lightBlue: #a3cef1;
#app {
  font-family: "Poppins", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  cursor: default;
  overflow: hidden;
}
body {
  &::-webkit-scrollbar {
    width: 0.5rem;
  }
  &::-webkit-scrollbar-track {
    background-color: $whiteBlue;
  }
  &::-webkit-scrollbar-thumb {
    background-color: $darkBlue;
    border-radius: 5px;
  }
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
a {
  text-decoration: none;
  cursor: pointer;
  opacity: 0.75;
  color: #2c3e50;
  transition: color 0.25s ease-in-out;

  &:hover {
    opacity: 1;
  }
}
nav,
ul,
li {
  list-style-type: none;
}
</style>
