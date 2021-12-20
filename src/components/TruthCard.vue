<template>
  <div class="truth-container">
    <h1>Verdade</h1>
  </div>
</template>

<script>
export default {};
</script>

<style lang="scss" scoped>
$lightBlue: #add8e6;

.truth-container {
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
  background-color: $lightBlue;
  color: rgb(44, 44, 44);

  &:hover {
    cursor: pointer;
  }
}
</style>
