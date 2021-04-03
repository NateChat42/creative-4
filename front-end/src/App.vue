<template>
  <div id="app">
    <div id="menu">
      <div id="brand">
        <router-link to="/">
          <h2>Manage Character Quests</h2>
        </router-link>
      </div>
      <router-link to="/tavern">
        <h2>Create Characters</h2>
      </router-link>
    </div>
    <router-view />
  </div>
</template>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 50px 100px;
  background: #555555;
}

#menu {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-column-gap: 5px;
  grid-template-areas: "none brand side";
  margin-bottom: 50px;
}

#menu a {
  color: #d85701;
}

#brand {
  grid-area: brand;
  display: flex-start;
  justify-content: center;
}

#brand img {
  height: 300px;
}

#side {
  grid-area: side;
  display: flex;
  justify-content: flex-end;
}

#side img {
  width: 50px;
}

.menu-item {
  display: flex;
  flex-direction: column;
}

.menu-item p {
  margin: 0px;
}

.browse {
  margin-right: 50px;
}

.plus {
  margin-right: 50px;
}

.plus-img {
  margin-left: 30px;
}
</style>