<template>
  <v-container fluid>
    <v-row>
      <v-col 
        cols="12" 
        md="12" 
        lg="6"
      >
        <!-- input card -->
        <v-card class="pa-lg-5">
          <v-form>
            <v-container>
              <v-row>
                <v-col
                  cols="12"
                >
                  <v-text-field
                    v-model="longUrl"
                    label="Paste Your URL"
                    required
                    outlined
                    v-on:keydown.enter.prevent="controlInput"
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-form>         
          <v-card-actions>
            <v-row
              align="center"
              justify="end"
            >
              <v-btn 
                @click="controlInput" 
                depressed
                class="mx-4 mb-3 mb-lg-0" 
              >
                Make it Short
              </v-btn>
            </v-row>
          </v-card-actions>
        </v-card>
        
        <!-- output card -->
        <v-card v-if="success" class="mt-5 px-5 grey--text">
          <v-card-title>Original Long URL</v-card-title>
          <v-card-text>
            <a 
              :href="shortUrl" 
              target="_blank" 
              class="blue--text"
            >
              {{ url }}
            </a>
          </v-card-text>
          <v-card-title>Shortened URL</v-card-title>
          <v-card-text>
            <a 
              :href="shortUrl" 
              target="_blank" 
              class="green--text"
            >
              {{ shortUrl }}
            </a>
          </v-card-text>            
          <v-card-actions>
            <v-row
              align="center"
              justify="end"
            >
              <v-btn 
                :href="shortUrl" 
                target="_blank" 
                depressed
                class="mb-4 ml-2" 
              >
                Test URL
              </v-btn>
              <v-btn 
                v-clipboard:copy="shortUrl"
                v-clipboard:success="onCopy"
                v-clipboard:error="onError"
                depressed
                class="mb-4 ml-2 mr-lg-4"
              >
                copy
              </v-btn>
            </v-row>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col 
        cols="12" 
        md="8" 
        sm="8" 
        lg="4" 
        offset-lg="2" 
        offset-sm="3" 
        class="mt-5 pa-5"
      >
        <v-img src="bg.svg" alt="background illustration" max-width="100%"></v-img>
      </v-col>
    </v-row>

    <!-- error notification-->
    <v-snackbar
      v-model="errorInput"
      color="error"
    >
      {{ error }}
      <v-btn
        color="white"
        icon
        @click="errorInput = false"
      >
        <v-icon>mdi-close</v-icon>
      </v-btn>
    </v-snackbar>

    <!-- success notification-->
    <v-snackbar
      v-model="successBar"
      :timeout="timeout"
      color="success"
    >
      {{ successMsg }}
    </v-snackbar>
  </v-container>
</template>

<script>
import axios from 'axios'

const BITLY_URL = 'https://api-ssl.bitly.com/v3/shorten?';
const BITLY_LOGIN = "o_4nm0b7lfsb";
const BITLY_KEY = "R_3acba2a6f39844c2adb685084688f6bd";

export default {
  data() {
    return {
      // long url input
      longUrl: '',
      // shortened url output
      shortUrl: '',
      // previous shortened url
      url: '',
      // notification controls
      success: false,
      successBar: false,
      successMsg: '',
      timeout: 2000,
      errorInput : false,
      error: 'Unknown Error! Please try again.'
    }
  },
  methods: {
    	// Method who control input 
			controlInput : function() {
        // if longUrl is not empty
				if(this.longUrl != '') {
          // if longUrl is valid 
          if(this.checkUrl(this.longUrl)) {

            // call to shorten longUrl
            this.shortenUrl();

            //Set errorInput to false
            this.errorInput = false;
            console.log("Valid URL");
          } else {
            // invalid URL handling
            this.success = false;
						this.errorInput = true;
            this.error="Please input a valid URL";
            console.log("Please input a valid URL");
          }
				} else {
          // empty URL handling
          this.success = false;
          this.errorInput = true;
          this.error="Please input an URL";
          console.log("Please input an URL");
				}
      },
      
      // Tests validity of an URL
      checkUrl : function(url) {
        return /^(https?|ftp):\/\/(((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:)*@)?(((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5]))|((([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.)+(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.?)(:\d*)?)(\/((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)+(\/(([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)*)*)?)?(\?((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)|[\uE000-\uF8FF]|\/|\?)*)?(\#((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)|\/|\?)*)?$/i.test(url);
      },

			// shortened URL handling
			addUrl : function() {
        // set previous URL 
        this.url = this.longUrl;
        // reset main input
				this.longUrl = '';
			},

			// shorten longUrl with bitly API
			shortenUrl : function() {
				var vm = this
				// send API request
				axios.get(BITLY_URL, {
					params : {
				        "format": "json",
				        "apiKey": BITLY_KEY,
				        "login": BITLY_LOGIN,
				        "longUrl": this.longUrl
				    }
			    })
				.then(function (response) {
					if(response.status == 200) {
						// set app vars
						vm.longUrl = response.data.data.long_url;
            vm.shortUrl = response.data.data.url;

            // pre-shortened URL handling 
            if(response.data.data.url == undefined) {
              vm.success = false;
              vm.errorInput = true;
              vm.error="Opps! Seems like it's already shortened.";
              console.log("Opps! Seems like it's already shortened.");
            } else {
              // success handling
              vm.addUrl();
              vm.success = true;
              vm.successBar = true;
              vm.successMsg = 'URL successfully shortened'  
            }
           
					} else{
						console.log('Opps dude, status code != 200 :( ')
          }
          // if(response.status == 400) {
          //   console.log("400")
          // }
          // if(response.status == 404) {
          //   console.log("404")
          // }
          // if(response.status == 422) {
          //   console.log("422")
          // }
          // if(response.status == 500) {
          //   console.log("500")
          // }
          // if(response.status == 503) {
          //   console.log("503")
          // }
				})
				.catch(function (error) {
					console.log('Error! ' + error);
				});
			},

			// reset input form
			clearInput : function() {
				this.longUrl = '';
				this.errorInput = false;
      },

      // generated shortUrl copied
      onCopy: function (e) {
        this.successBar = true;
        this.successMsg="URL copied to clipboard"
        console.log('You just copied: ' + e.text)
      },
      // unable to copy generated shortUrl
      onError: function (e) {
        this.errorInput = true;
        this.error = 'Failed to copy shortened URL!'
        console.log('Failed to copy shortened URL!')

      },
  }
}
</script>
