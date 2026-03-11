<script setup lang="ts">
import { ref } from 'vue';
import InputText from 'primevue/inputtext';

const name = ref<string>('');
</script>

<template>
  <div style="max-width: 600px; margin: 40px auto; font-family: system-ui, sans-serif">
    <h2>InputText emit <code>| undefined</code> — False Positive</h2>

    <p>
      <code>InputText</code> declares <code>string | undefined</code> in its
      <code>update:modelValue</code> emit type, but it only ever emits
      <code>string</code> at runtime. With <code>strictTemplates: true</code>,
      <code>vue-tsc</code> rejects this <code>v-model</code> binding to
      <code>Ref&lt;string&gt;</code>.
    </p>

    <InputText v-model="name" placeholder="Type something..." style="width: 100%" />

    <p v-if="name" style="margin-top: 16px; color: #334155">
      You typed: <strong>{{ name }}</strong>
    </p>

    <div style="margin-top: 24px; padding: 16px; background: #f8fafc; border-radius: 8px">
      <h3 style="margin: 0 0 8px">
        Run <code>bun run type-check</code> in the terminal
      </h3>
      <p style="margin: 0; color: #64748b">
        <strong>Without patch:</strong> TS2322 —
        <code>Type 'string | undefined' is not assignable to type 'string'</code><br />
        <strong>With patch:</strong> 0 errors
      </p>
    </div>
  </div>
</template>
