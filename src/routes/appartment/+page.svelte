<script>
  import { onMount } from 'svelte';
  import { PUBLIC_BASE_URL } from "$env/static/public";
  import axios from 'axios';
  let age = '';
  let bmi = '';
  let avg_glucose_level = '';
  let work_type = '';
  let smoking_status = '';
  let prediction = null;
  let probability = null;
  let error = null;

  const submitForm = async () => {
      try {
          const apiUrl = `${PUBLIC_BASE_URL}/api/predict/`;
          console.log("API URL:", apiUrl); // Debug-Ausgabe der API-URL
          console.log("Parameters:", {
              age: age,
              bmi: bmi,
              avg_glucose_level: avg_glucose_level,
              work_type_Govt_job: work_type === 'Govt_job' ? 1 : 0,
              work_type_Never_worked: work_type === 'Never_worked' ? 1 : 0,
              work_type_Private: work_type === 'Private' ? 1 : 0,
              work_type_Self_employed: work_type === 'Self_employed' ? 1 : 0,
              work_type_children: work_type === 'children' ? 1 : 0,
              smoking_status_formerly_smoked: smoking_status === 'formerly smoked' ? 1 : 0,
              smoking_status_never_smoked: smoking_status === 'never smoked' ? 1 : 0,
              smoking_status_smokes: smoking_status === 'smokes' ? 1 : 0
          }); // Debug-Ausgabe der Parameter

          const response = await axios.get(apiUrl, {
              params: {
                  age: age,
                  bmi: bmi,
                  avg_glucose_level: avg_glucose_level,
                  work_type_Govt_job: work_type === 'Govt_job' ? 1 : 0,
                  work_type_Never_worked: work_type === 'Never_worked' ? 1 : 0,
                  work_type_Private: work_type === 'Private' ? 1 : 0,
                  work_type_Self_employed: work_type === 'Self_employed' ? 1 : 0,
                  work_type_children: work_type === 'children' ? 1 : 0,
                  smoking_status_formerly_smoked: smoking_status === 'formerly smoked' ? 1 : 0,
                  smoking_status_never_smoked: smoking_status === 'never smoked' ? 1 : 0,
                  smoking_status_smokes: smoking_status === 'smokes' ? 1 : 0
              }
          });
          prediction = response.data.prediction;
          probability = response.data.probability;
          error = null;
      } catch (err) {
          error = 'Fehler bei der Vorhersage';
          console.error("Error:", err); // Ausgeben des Fehlers in der Konsole
      }
  };
</script>

<main>
  <div class="container">
      <div class="image-container">
          <img src="/image.png" alt="Futuristic Healthcare Setting" />
      </div>
      <div class="form-container">
          <h1>Schlaganfall Vorhersage</h1>
          <form on:submit|preventDefault={submitForm}>
              <div class="input-row">
                  <div class="input-group">
                      <label for="age">Alter in Jahren</label>
                      <input type="number" id="age" bind:value={age} required />
                  </div>
                  <div class="input-group">
                      <label for="bmi">BMI</label>
                      <input type="number" step="0.1" id="bmi" bind:value={bmi} required />
                  </div>
              </div>
              <div class="input-row">
                  <div class="input-group">
                      <label for="avg_glucose_level">⌀-Glukosespiegel</label>
                      <input type="number" step="0.1" id="avg_glucose_level" bind:value={avg_glucose_level} required />
                  </div>
                  <div class="input-group">
                      <label for="work_type">Arbeitstyp</label>
                      <select id="work_type" bind:value={work_type} required>
                          <option value="">Bitte auswählen</option>
                          <option value="Govt_job">Öffentlicher Dienst</option>
                          <option value="Never_worked">Nie gearbeitet</option>
                          <option value="Private">Privatwirtschaft</option>
                          <option value="Self_employed">Selbstständig</option>
                          <option value="children">Kinder</option>
                      </select>
                  </div>
              </div>
              <div class="input-row">
                  <div class="input-group">
                      <label for="smoking_status">Raucherstatus</label>
                      <div class="radio-group">
                          <label>
                              <input type="radio" bind:group={smoking_status} value="formerly smoked" />
                              Ehemals geraucht
                          </label>
                          <label>
                              <input type="radio" bind:group={smoking_status} value="never smoked" />
                              Nie geraucht
                          </label>
                          <label>
                              <input type="radio" bind:group={smoking_status} value="smokes" />
                              Raucht
                          </label>
                      </div>
                  </div>
              </div>
              <button type="submit">Vorhersage machen</button>
          </form>

          {#if error}
              <p class="error-message">{error}</p>
          {/if}

          {#if prediction !== null}
              <div class="result {prediction === 1 ? 'stroke' : 'no-stroke'}">
                  <p>Besteht ein Risiko für einen Schlaganfall?</p>
                  <p class="prediction">{prediction === 1 ? 'Ja' : 'Nein'}</p>
                  <div class="probabilities">
                      <p>Kein Schlaganfall: {Math.round(probability[0] * 100)} %</p>
                      <p>Schlaganfall: {Math.round(probability[1] * 100)} %</p>
                  </div>
              </div>
          {/if}
      </div>
  </div>
</main>

<style>
    main {
        text-align: center;
        padding: 2em;
        margin: 0 auto;
        max-width: 1200px;
        background-color: #36147f4f;
        border-radius: 10px;
        box-shadow: 0 0 10px rgba(46, 2, 77, 0.1);
        color: #2e195d;
    }

    .container {
        display: flex;
        flex-direction: row;
        align-items: flex-start;
        justify-content: center;
    }

    .image-container {
        flex: 1;
        display: flex;
        align-items: center;
        justify-content: center;
        padding: 1em;
    }

    .image-container img {
        max-width: 100%;
        height: auto;
        border-radius: 10px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    .form-container {
        flex: 1;
        padding: 2em;
        background-color: rgb(255, 255, 255);
        border-radius: 10px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    h1 {
        color: #2e195d;
        margin-bottom: 1em;
    }

    form {
        display: grid;
        gap: 1em;
    }

    .input-row {
        display: flex;
        justify-content: space-between;
        gap: 1em;
    }

    .input-group {
        flex: 1;
        display: flex;
        flex-direction: column;
        margin-bottom: 1em;
    }

    label {
        font-weight: bold;
        margin-bottom: 0.5em;
    }

    input, select {
        padding: 0.5em;
        font-size: 1em;
        border: 1px solid #ccc;
        border-radius: 5px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        transition: border-color 0.3s;
        color: #2e195d;
    }

    input:focus, select:focus {
        border-color: #2e195d;
        outline: none;
    }

    .radio-group {
        display: flex;
        gap: 1em;
        justify-content: center;
    }

    .radio-group label {
        display: flex;
        align-items: center;
        font-weight: normal;
    }

    .radio-group input {
        margin-right: 0.5em;
    }

    button {
        padding: 0.75em;
        font-size: 1em;
        background-color: #2e195d;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    button:hover {
        background-color: #5f00b3;
    }

    .error-message {
        color: red;
        margin-top: 1em;
    }

    .result {
        margin-top: 2em;
        padding: 1em;
        border-radius: 5px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .result.no-stroke {
        background-color: #e9f7ef;
    }

    .result.stroke {
        background-color: #f8d7da;
        color: #721c24;
    }

    .result .prediction {
        font-size: 1.5em;
        font-weight: bold;
    }

    .probabilities {
        text-align: left;
    }

    .probabilities p {
        margin: 0.5em 0;
    }
</style>
