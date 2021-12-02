<template>
  <div class="md:flex-shrink-0">
    <img
      class="h-48 w-full object-contain md:h-full md:w-48"
      alt="Hangman Image"
      :src="require('@/assets/cicaGlisha_' + brojSlike + '.jpg')"
    />
  </div>
</template>

<script>
export default {
  name: "SlikaVjesala",
  props: {
    brojSlike: Number,
  },
};
</script>
