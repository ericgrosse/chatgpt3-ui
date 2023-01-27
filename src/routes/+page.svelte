<script>
  import axios from 'axios';
  import { API_KEY } from '../components/ApiKey.js';
  import { toast } from '@zerodevx/svelte-toast'
  import { Stretch } from 'svelte-loading-spinners';

  let apiCallInProgress = false;
  let chatGPT3Response = '';

  async function handleClick() {
    try {
      apiCallInProgress = true;

      const response = await axios.post(
        'https://api.openai.com/v1/completions',
        {
          model: 'text-davinci-003', // string, required
          prompt: '', // string or array, optional, defaults to <|endoftext|>
          suffix: null, // string, optional, defaults to null
          max_tokens: 150, // integer, optional, defaults to 16
          temperature: 1, // number, optional, defaults to 1
          top_p: 1, // number, optional, defaults to 1
          n: 1, // integer, optional, defaults to 1
          stream: false, // boolean, optional, defaults to false
          logprobs: null, // integer, optional, defaults to null
          echo: false, // boolean, optional, defaults to false
          stop: null, // string or array, optional, defaults to null
          presence_penalty: 0, // number, optional, defaults to 0
          frequency_penalty: 0, // number, optional, defaults to 0
          best_of: 1, // integer, optional, defaults to 1
          //logit_bias: null, // map, optional, defaults to null
          //user: null, // string, optional
        },
        {
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${API_KEY}`,
          },
        });
      
      console.log(response.data.choices[0].text);
      chatGPT3Response = response.data.choices[0].text;
      
      apiCallInProgress = false;

      toast.push('Successfully generated text', {
        theme: {
          '--toastColor': 'mintcream',
          '--toastBackground': 'rgba(72,187,120,0.9)',
          '--toastBarBackground': '#2F855A'
        }
      });

  } catch (error) {
      console.log('Inside error handler');
      console.log(error);

      apiCallInProgress = false;

      let errorMessage = error.message;

      if (
        error.response &&
        error.response.data &&
        error.response.data.error &&
        error.response.data.error.message
      ) {
        errorMessage = error.response.data.error.message;
      }

      toast.push(errorMessage, {
        theme :{
          '--toastColor': 'mintcream',
          '--toastBackground': 'rgba(187,72,72,0.9)',
          '--toastBarBackground': '#A10000'
        }
      });
    }
  }
  </script>
  
  <main class="container">
    <h1>ChatGPT3 UI</h1>

    {#if apiCallInProgress}
      <div>
        <p>Generating Response...</p>
        <Stretch size="60" color="#FF3E00" unit="px" duration="1s" />
      </div>
    {/if}

    {#if !apiCallInProgress}
      <h3>Required Fields</h3>
      <input type="text" placeholder="prompt" />
      <h3>Optional Fields</h3>

      <div class="optional-fields-container">
        <div class="optional-fields">
          <input type="text" placeholder="suffix" />
          <input type="text" placeholder="max_tokens" />
          <input type="text" placeholder="temperature" />
        </div>
        <div class="optional-fields">
          <input type="text" placeholder="top_n" />
          <input type="text" placeholder="n" />
          <input type="text" placeholder="stream" />
        </div>
        <div class="optional-fields">
          <input type="text" placeholder="logprobs" />
          <input type="text" placeholder="echo" />
          <input type="text" placeholder="stop" />
        </div>
        <div class="optional-fields">
          <input type="text" placeholder="presence_penalty" />
          <input type="text" placeholder="frequency_penalty" />
          <input type="text" placeholder="best_of" />
        </div>
      </div>

      <button class="generate-short-story" on:click={handleClick}>Generate Text</button>
      <p>Output</p>
      <p>{chatGPT3Response}</p>
    {/if}
  </main>
  
  <style>
    .generate-short-story {
      background-color: #2196f3;
      color: white;
      padding: 12px 24px;
      border-radius: 4px;
      font-size: 16px;
      text-align: center;
      text-decoration: none;
      display: inline-block;
      transition-duration: 0.4s;
      cursor: pointer;
      box-shadow: 0px 1px 3px 0px rgba(0, 0, 0, 0.2),
                  0px 1px 1px 0px rgba(0, 0, 0, 0.14), 0px 2px 1px -1px rgba(0, 0, 0, 0.12);
    }

    .generate-short-story:hover {
      background-color: #1976d2;
    }

    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }
  </style>