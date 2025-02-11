<template>
  <q-layout view="lHh Lpr lFf">
    <q-page-container class="home-container">
      <q-btn flat to="/login" class="get-started">Get Started</q-btn>
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts"></script>

<style>
.home-container {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #1d1d1f;
}
.get-started {
  background-color: #f5f5f7;
  font-size: 1.2rem;
  border-radius: 12px;
}
</style>
