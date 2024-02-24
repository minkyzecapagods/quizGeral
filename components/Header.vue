<template>
  <div>
    <nav id="cabecalho">
      <h1 id="titulo">Quiz do Ouriço</h1>
      <img id="img" src='/soniclogo.png'>
    </nav>
  </div>
</template>

<style>
#cabecalho {
  background-color: #201658;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  padding: 20px;
  padding-left: 40px;
  min-height: 80px;
  display: flex;
  align-items: center;
  color: aliceblue;
  box-shadow: 0px 10px 30px rgba(0, 0, 0, 0.2);
  align-self: center;
  cursor: pointer;
  transition: all 0.5s;
}

#cabecalho:hover {
  background-color: #0F1035;
}

#img {
  height: 80px;
  cursor: pointer;
}

#titulo {
  font-size: 40px;
  margin-right: 20px;
}
</style>
