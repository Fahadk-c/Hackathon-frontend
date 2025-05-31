<template>
  <!-- Your component template content -->
   Home
</template>

<script setup>
// Inline middleware using definePageMeta
definePageMeta({
  middleware: [
    function ({ redirect }) {
      return redirect('/app')
    }
  ]
})
</script>