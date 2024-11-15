<script>

export default {
  name: 'header',
};

</script>

<template>
  <header>
    <img src="../../public/guessthestepsCircle.png" alt="">
    <h1>Benvenuto su <span>Guess the steps!</span></h1>
</header>
</template>

<style lang="scss" scoped>
header {
    display: flex;
    align-items: center;
    margin-bottom: 2em;
    height: 5rem;

    img {
        width: 5rem;
    }

    h1 {
        margin: 0 0 0 1rem;
    }

    span {
        color: #42b883;
    }
}

</style>