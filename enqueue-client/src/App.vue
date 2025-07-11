<template>
  <div style="max-width: 400px; margin: 2rem auto; font-family: Arial, sans-serif;">
    <h1>Wyślij maila (enqueue)</h1>
    <form @submit.prevent="sendEmail">
      <div>
        <label for="email">Email odbiorcy:</label><br />
        <input id="email" type="email" v-model="email" required placeholder="adres@example.com" />
      </div>
      <div style="margin-top: 1rem;">
        <label for="subject">Temat:</label><br />
        <input id="subject" type="text" v-model="subject" required placeholder="Temat maila" />
      </div>
      <div style="margin-top: 1rem;">
        <label for="message">Treść wiadomości:</label><br />
        <textarea id="message" v-model="message" required rows="4" placeholder="Treść maila"></textarea>
      </div>
      <button class="button" type="submit" style="margin-top: 1rem;">Wyślij</button>
    </form>

    <p v-if="responseMessage" style="color: green; margin-top: 1rem;">{{ responseMessage }}</p>
    <p v-if="errorMessage" style="color: red; margin-top: 1rem;">{{ errorMessage }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue';
const API_URL = import.meta.env.VITE_API_URL;
const email = ref('');
const subject = ref('');
const message = ref('');
const responseMessage = ref('');
const errorMessage = ref('');

async function sendEmail() {
  responseMessage.value = '';
  errorMessage.value = '';

  const payload = {
    type: 'send_email',
    to: email.value,
    subject: subject.value,
    body: message.value,
  };

  try {
    const res = await fetch('https://projektpwo-breuctarbpdde0bu.polandcentral-01.azurewebsites.net/api/enqueue', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(payload),
    });

    if (!res.ok) {
      throw new Error(`Błąd serwera: ${res.statusText}`);
    }

    const data = await res.json();
    responseMessage.value = data.message || 'Zadanie wysłane do kolejki.';

    email.value = '';
    subject.value = '';
    message.value = '';
  } catch (error) {
    errorMessage.value = error.message || 'Wystąpił błąd podczas wysyłania.';
  }
}
</script>

<style scoped>
body, html, #app {
  height: 100%;
  margin: 0;
  background: linear-gradient(135deg, #a8d0ff 0%, #f0f8ff 100%);
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.container {
  max-width: 520px;
  margin: 5rem auto;
  background: #ffffffdd;
  padding: 3rem 2.8rem;
  border-radius: 14px;
  box-shadow: 0 10px 25px rgba(0, 105, 255, 0.25);
  transition: box-shadow 0.3s ease;
}

.container:hover {
  box-shadow: 0 14px 40px rgba(0, 105, 255, 0.38);
}

h1 {
  margin-bottom: 2.5rem;
  text-align: center;
  color: #0047b3;
  font-weight: 700;
  font-size: 2.3rem;
  letter-spacing: 0.04em;
}

.form {
  display: flex;
  flex-direction: column;
}

label {
  margin-top: 1.5rem;
  margin-bottom: 0.7rem;
  font-weight: 600;
  color: #003580;
  font-size: 1.25rem;
  user-select: none;
}

input,
textarea {
  padding: 1.6rem 1.8rem;
  font-size: 1.5rem;
  border: 2px solid #8ab4f8;
  border-radius: 12px;
  background-color: #f8fbff;
  color: #001f4d;
  box-shadow: inset 0 3px 6px rgba(102, 147, 255, 0.15);
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
  font-weight: 500;
  resize: vertical;
}

input:focus,
textarea:focus {
  outline: none;
  border-color: #1a56db;
  box-shadow: 0 0 18px #1a56dbaa;
  background-color: #ffffff;
}

button {
  margin-top: 3.5rem;
  background: linear-gradient(135deg, #1a56db 0%, #3f82f7 100%);
  color: white;
  font-size: 1.7rem;
  font-weight: 700;
  border: none;
  border-radius: 16px;
  cursor: pointer;
  box-shadow: 0 8px 24px rgba(26, 86, 219, 0.7);
  transition: background 0.3s ease, box-shadow 0.3s ease;
  user-select: none;
}

button:hover {
  background: linear-gradient(135deg, #1448a0 0%, #2a66db 100%);
  box-shadow: 0 10px 32px rgba(26, 86, 219, 0.9);
}

.success {
  margin-top: 1.8rem;
  color: #0a7cff;
  font-weight: 600;
  text-align: center;
  font-size: 1.25rem;
  letter-spacing: 0.02em;
  user-select: none;
}

.error {
  margin-top: 1.8rem;
  color: #d92323;
  font-weight: 600;
  text-align: center;
  font-size: 1.25rem;
  letter-spacing: 0.02em;
  user-select: none;
}
</style>
