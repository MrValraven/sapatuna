<template>
  <div class="dare-container">
    <h1>Consequência</h1>
  </div>
</template>

<script>
export default {};
</script>

<style lang="scss" scoped>
$sexyRed: #e62020;
.dare-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 400px;
  width: 300px;
  padding: 20px 30px;
  margin-right: 75px;
  border-radius: 10px;
  background-color: rgba(255, 255, 255, 0.4);
  box-shadow: 0 10px 10px 10px rgba(0, 0, 0, 0.2);
  text-transform: uppercase;
  background-color: $sexyRed;
  color: rgb(248, 224, 224);

  &:hover {
    cursor: pointer;
  }
}
</style>
