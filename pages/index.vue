<template>
  <v-container fluid>
    <v-row>
      <v-col cols="12" md="12" lg="6">
        <v-card class="pa-lg-5">
          <v-form>
            <v-container>
              <v-row>
                <v-col
                  cols="12"
                >
                  <v-text-field
                    v-model="longUrl"
                    label="Paste Your Long URL"
                    required
                    outlined
                    v-on:keydown.enter.prevent="controlDatas"
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
              <v-btn class="mx-4 mb-3 mb-lg-0" @click="controlDatas" depressed>Make it Short</v-btn>
            </v-row>
          </v-card-actions>
        </v-card>
        <!-- O/P -->
        <v-card v-if="success" class="mt-5 px-5 grey--text">
          <v-card-title>Original Long URL</v-card-title>
          <v-card-text>
            <a :href="shortUrl" target="_blank" class="blue--text">{{ url }}</a>
          </v-card-text>
          <v-card-title>Shortened URL</v-card-title>
          <v-card-text>
            <a :href="shortUrl" target="_blank" class="green--text">{{ shortUrl }}</a>
          </v-card-text>            
          
          <v-card-actions>
            
            <v-row
              align="center"
              justify="end"
            >
              <v-btn class="mb-4 ml-2" :href="shortUrl" target="_blank" depressed>Test URL</v-btn>
              <v-btn 
                v-clipboard:copy="shortUrl"
                v-clipboard:success="onCopy"
                v-clipboard:error="onError"
                class="mb-4 ml-2 mr-lg-4"
                depressed
              >
                copy
              </v-btn>
            </v-row>
            
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col cols="12" md="8" sm="8" lg="4" offset-lg="2" offset-sm="3" class="mt-5 pa-5">
         <v-img src="bg.png" max-width="100%"></v-img>
      </v-col>
    </v-row>

    

    <!-- error -->
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

    <!-- success -->
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
// import Logo from '~/components/Logo.vue'
// import VuetifyLogo from '~/components/VuetifyLogo.vue'
import axios from 'axios'
const BITLY_URL = 'https://api-ssl.bitly.com/v3/shorten?';
const BITLY_LOGIN = "o_4nm0b7lfsb";
const BITLY_KEY = "R_3acba2a6f39844c2adb685084688f6bd";

export default {
  components: {
    // Logo,
    // VuetifyLogo
  },
  data() {
    return {
      // long url input
      longUrl: '',
      // urlRules: [
      //   v => !!v || 'URL is required',
      //   v => v.length >= 5 || 'URL must be greater than 5 characters',
      // ],
      // shortened url output
      url: '',
      shortUrl: '',
      // isLoading: false,
      // Error control
      success: false,
      successBar: false,
      successMsg: '',
      timeout: 2000,
      errorInput : false,
      error: 'Unexpected Error!'
    }
  },
  methods: {
   
    	// Method who control input and call others methods
			controlDatas : function() {
        console.log("controldatas");

				// If longUrl is not empty & valid url
				if(this.longUrl != '') {

          if(this.checkUrl(this.longUrl)) {

            // this.isLoading = true;

            // Call addUrl method
            this.cropUrl();

            //Set errorInput to false
            this.errorInput = false;
            // this.success = true;
            console.log("controldatas success");
          } else {
            this.success = false;
						this.errorInput = true;
            this.error="Please input a valid URL";
            console.log("Please input a valid URL");
          }
				} else {

          // Set errorInput to true
          this.success = false;
          this.errorInput = true;
          this.error="Please input a URL";
          console.log("Please input a URL");
				}
      },
      
      // Test if a string is an url
      checkUrl : function(url) {
        return /^(https?|ftp):\/\/(((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:)*@)?(((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5]))|((([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.)+(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.?)(:\d*)?)(\/((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)+(\/(([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)*)*)?)?(\?((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)|[\uE000-\uF8FF]|\/|\?)*)?(\#((([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(%[\da-f]{2})|[!\$&'\(\)\*\+,;=]|:|@)|\/|\?)*)?$/i.test(url);
      },


			// Add the new url into results table
			addUrl : function() {

				// Push the new cropped url into the results table
				// this.shortUrl=,

        // Reset main input
        this.url = this.longUrl;
				this.longUrl = '';
				
				// Set isLoading to false
				// this.isLoading = false;
			},

			// Crop URL with tinyurl API
			cropUrl : function() {
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
            
            if(response.data.data.url == undefined) {
              vm.success = false;
              vm.errorInput = true;
              vm.error="Opps! Seems like its already shortened.";
              console.log("Opps! Seems like its already shortened.");
            } else {
               // Call addUrl method
              vm.addUrl();
              vm.success = true;
              vm.successBar = true;
              vm.successMsg = 'URL successfully shortened'  
            }
           
					} else{
						console.log('Opps dude, status code != 200 :( ')
          }
          if(response.status == 422) {
            alert("400")
          }
          if(response.status == 422) {
            alert("404")
          }
          if(response.status == 422) {
            alert("422")
          }
          if(response.status == 422) {
            alert("500")
          }
          if(response.status == 422) {
            alert("503")
          }
				})
				.catch(function (error) {
					console.log('Error! ' + error);

					// Set isLoading to false
					// this.isLoading = false;
				});
			},

			// Reset form
			clearInput : function() {
				this.longUrl = '';
				this.errorInput = false;
      },
        onCopy: function (e) {
        console.log('You just copied: ' + e.text)
        this.successBar = true;
        this.successMsg="URL copied to clipboard"
      },
      onError: function (e) {
        this.errorInput = true;
        this.error = 'Failed to copy shortened URL!'
        console.log('Failed to copy shortened URL!')

      },
  }
}
</script>
