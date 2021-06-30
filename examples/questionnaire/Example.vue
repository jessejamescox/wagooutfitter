// Create and setup your form here
 
<template>
  <div>
    <header class="vff-header">
      <div class="f-container">
       <!-- Add custom logo here -->
        <img src = "/wago.svg" alt="WAGO Logo" width="150" height="100"/>
      </div>
    </header>

    <flow-form
      ref="flowform"
      v-on:complete="onComplete"
      v-on:submit="onSubmit"
      v-bind:questions="questions"
      v-bind:language="language"
      v-bind:standalone="true"
    >
    <!-- Custom content for the Complete/Submit screen slots in the FlowForm component -->
      <!-- We've overriden the default "complete" slot content -->
     <template v-slot:complete>
        <div class="f-section-wrap">
          <p>
            <span class="fh2">Thank you. 🙏</span>
            <span class="f-section-text">
              Great work, the survey is completed, and our demo is done. You can review your answers or press submit.
            </span>
          </p>
          <p class="f-description">Note: No data will be saved and/or sent in this demo.</p>
        </div>  
      </template>

      <!-- We've overriden the default "completeButton" slot content -->
      <template v-slot:completeButton>
        <div class="f-submit" v-if="!submitted">
          <button 
            class="o-btn-action"
            ref="button"
            type="submit"
            href="#"
            v-on:click.prevent="onSendData()"
            aria-label="Press to submit"
          >
            <span>{{ language.submitText }}</span>
          </button>
          <a class="f-enter-desc"
            href="#"
            v-on:click.prevent="onSendData()"
            v-html="language.formatString(language.pressEnter)">
          </a>
        </div>

        <p class="text-success" v-if="submitted">Submitted succesfully.</p>
      </template>
    </flow-form>
  </div>
</template>

<script>
  /*
    Copyright (c) 2020 - present, DITDOT Ltd. - MIT Licence
    https://www.ditdot.hr/en
  */

  // Import necessary components and classes
  import FlowForm from '../../src/components/FlowForm.vue'
  import QuestionModel, { QuestionType, ChoiceOption, LinkOption } from '../../src/models/QuestionModel'
  import LanguageModel from '../../src/models/LanguageModel'

  export default {
    name: 'example',

    components: {
      FlowForm
    },

    data() {
      return {
        submitted: false,
        completed: false,
        language: new LanguageModel(),
        // Create question list with QuestionModel instances
        questions: [
          new QuestionModel({
            id: 'first_name',
            tagline: 'Hi! Welcome to the WAGO Platform Outfitter 😊',
            title: 'What is your name?',
            type: QuestionType.Text,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'organization',
            tagline: "Nice to meet you 👀, let's continue",
            title: 'Provide an organization name',
            type: QuestionType.Text,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'email',
            tagline: "Great 😊, please provide an email address",
            title: 'Provide an  email.',
            type: QuestionType.Email,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'multiple_choice_image',
            tagline: "Let's start configuring...",
            title: 'Tell us what platform you are configuring.',
            helpTextShow: false,
            type: QuestionType.MultiplePictureChoice,
            multiple: false,
            required: true,
            options: [
              new ChoiceOption({
                imageSrc: require('./assets/images/PFC100.png'),
                imageAlt: 'PFC100 logo',
                label: 'PFC100 / PFC200 (Gen1)'
              }),
              new ChoiceOption({
                imageSrc: require('./assets/images/PFC200.png'),
                imageAlt: 'Twitter logo',
                label: 'PFC200 (Gen2)'
              }),
              new ChoiceOption({
                imageSrc: require('./assets/images/EDGE.png'),
                imageAlt: 'Instagram logo',
                label: 'Edge Controller'
              }),
              new ChoiceOption({
                imageSrc: require('./assets/images/TP600.png'),
                imageAlt: 'TikTok logo',
                label: 'TP600'
              }),
            ]
          }),
          new QuestionModel({
            id: 'runtime',
            title: 'Which runtime will you be using?',
            helpTextShow: false,
            type: QuestionType.MultipleChoice,
            multiple: false,
            allowOther: true,
            required: true,
            options: [
              new ChoiceOption({
                label: 'e!RUNTIME'
              }),
              new ChoiceOption({
                label: 'Codesys Control Runtime'
               })
            ]
          }),
          new QuestionModel({
            id: 'docker',
            tagline: 'Will you require Docker Engine? 🐳',
            title: 'Yes or No:',
            type: QuestionType.Dropdown,
            multiple: false,
            placeholder: 'Select',
            inline: true,
            required: true,
            options: [
              new ChoiceOption({
                label: 'Nope'
              }),
              new ChoiceOption({
                label: 'Yep',
                value: 'path_b'
              })
            ],
            jump: {
              path_b: 'path_b'
            }
          }),
          new QuestionModel({
            id: 'path_a',
            title: 'OK, Maybe you dont need an outfitter after all',
            content: 'Press enter or use the continue button for the final submit screen.',
            type: QuestionType.SectionBreak,
            jump: {
              _other: '_submit'
            }
          }),
          new QuestionModel({
            id: 'path_b',
            tagline: 'Path B',
            title: 'Rad!  Do you want to pre-install some Docker images?',
            helpText: 'Pick from the recommendations below',
            type: QuestionType.MultipleChoice,
            multiple: true,
            required: false,
            options: [
              new ChoiceOption({
                label: 'Node-Red',
                value: 'docker_image_nodered'
              }),
              new ChoiceOption({
                label: 'InfluxDB',
                value: 'docker_image_influxdb'
              }),
              new ChoiceOption({
                label: 'Grafana',
                value: 'docker_image_grafana'
              }),
              new ChoiceOption({
                label: 'Kbus Modbus Slave',
                value: 'docker_image_kbusmodbus'
              }),
              new ChoiceOption({
                label: 'InfluxDB',
                value: 'docker_image_influxdb'
              }),
              new ChoiceOption({
                label: 'Python',
                value: 'docker_image_python'
              })
            ],
            jump: {
              path_a: 'path_a'
            }
          })
        ]
      }
    },

    mounted() {
      document.addEventListener('keyup', this.onKeyListener)
    },

    beforeUnmount() {
      document.removeEventListener('keyup', this.onKeyListener)
    },

    methods: {
      onKeyListener($event) {
        // We've overriden the default "complete" slot so
        // we need to implement the "keyup" listener manually.

        if ($event.key === 'Enter' && this.completed && !this.submitted) {
          this.onSendData()
        }
      },

      /* eslint-disable-next-line no-unused-vars */
      onComplete(completed, questionList) {
        // This method is called whenever the "completed" status is changed.
        this.completed = completed
      },

      /* eslint-disable-next-line no-unused-vars */
      onSubmit(questionList) {
        // This method will only be called if you don't override the
        // completeButton slot.
        console.log("Got Here")
        this.onSendData()
      },
      
      onSendData() {
        // Set `submitted` to true so the form knows not to allow back/forward
        // navigation anymore.
        this.$refs.flowform.submitted = true

        this.submitted = true

        /* eslint-disable-next-line no-unused-vars */
        const data = this.getData()
          //You can use Fetch API to send the data to your server, eg.:
          let mydata = JSON.stringify(data)
          console.log(mydata)
          /*fetch(url, {
             method: 'POST',
             headers: {
               'Content-Type': 'application/json'
             },
             body: JSON.stringify(data)
           })
           */
      },

      getData() {
        const data = {
          questions: [],
          answers: []
        }

        this.questions.forEach(question => {
          if (question.title) {
            let answer = question.answer
            if (Array.isArray(answer)) {
              answer = answer.join(', ')
            }

            data.questions.push(question.title)
            data.answers.push(answer)
          }
        })

        return data
      }
    }
  }
</script>

<style lang="css">
  @import '../../src/assets/css/themes/theme-minimal.css';
  /* If using the npm package, use the following lines instead of the one above */
  /* @import '~@ditdot-dev/vue-flow-form/dist/vue-flow-form.css'; */
  /* @import '~@ditdot-dev/vue-flow-form/dist/vue-flow-form.theme-minimal.css'; */
</style>