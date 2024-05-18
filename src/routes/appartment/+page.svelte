<script>
	import { onMount } from 'svelte';
	import axios from 'axios';
	let age = '';
	let bmi = '';
	let avg_glucose_level = '';
	let hypertension = '';
	let heart_disease = '';
	let ever_married = '';
	let prediction = null;
	let probability = null;
	let error = null;
  
	const submitForm = async () => {
	  try {
		const response = await axios.get('http://localhost:5000/api/predict/', {
		  params: {
			age: age,
			bmi: bmi,
			avg_glucose_level: avg_glucose_level,
			hypertension: hypertension,
			heart_disease: heart_disease,
			ever_married: ever_married,
		  }
		});
		prediction = response.data.prediction;
		probability = response.data.probability;
		error = null;
	  } catch (err) {
		error = 'Fehler bei der Vorhersage';
		console.error(err);
	  }
	};
  </script>
  
  <main>
	<h1>Schlaganfall Vorhersage</h1>
	<form on:submit|preventDefault={submitForm}>
	  <div>
		<label for="age">Alter:</label>
		<input type="number" id="age" bind:value={age} required />
	  </div>
	  <div>
		<label for ="bmi">BMI:</label>
		<input type="number" step="0.1" id="bmi" bind:value={bmi} required />
	  </div>
	  <div>
		<label for="avg_glucose_level">Durchschnittlicher Glukosespiegel:</label>
		<input type="number" step="0.1" id="avg_glucose_level" bind:value={avg_glucose_level} required />
	  </div>
	  <div>
		<label for="hypertension">Bluthochdruck (0 oder 1):</label>
		<input type="number" id="hypertension" bind:value={hypertension} required />
	  </div>
	  <div>
		<label for="heart_disease">Herzkrankheit (0 oder 1):</label>
		<input type="number" id="heart_disease" bind:value={heart_disease} required />
	  </div>
	  <div>
		<label for="ever_married">Verheiratet (0 oder 1):</label>
		<input type="number" id="ever_married" bind:value={ever_married} required />
	  </div>
	  <button type="submit">Vorhersage machen</button>
	</form>
  
	{#if error}
	  <p style="color: red;">{error}</p>
	{/if}
  
	{#if prediction !== null}
	  <h2>Vorhersage Ergebnis</h2>
	  <p>Vorhersage: {prediction === 1 ? 'Schlaganfall' : 'Kein Schlaganfall'}</p>
	  <p>Wahrscheinlichkeiten:</p>
	  <ul>
		<li>Kein Schlaganfall: {probability[0]}</li>
		<li>Schlaganfall: {probability[1]}</li>
	  </ul>
	{/if}
  </main>
  
  <style>
	main {
	  text-align: center;
	  padding: 1em;
	  margin: 0 auto;
	  max-width: 600px;
	}
	form {
	  display: grid;
	  gap: 0.5em;
	}
	div {
	  display: flex;
	  flex-direction: column;
	}
	label {
	  font-weight: bold;
	}
	button {
	  padding: 0.5em;
	  font-size: 1em;
	}
  </style>
  