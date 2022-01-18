<template>
  <Experiment title="Mental Rotation Experiment">
    <InstructionScreen :title="'Welcome'">
      Hello, thank you for being part of this experiment.
      <br />
      <br />
      The following experiment is called "mental rotation". 
      <br /> 
      It will test your ability for spatial thinking. 
      <br /> 
      <br /> 
      After clicking 'NEXT', you will see some general instructions telling you everything you need to complete the experiment.
    </InstructionScreen>

    <InstructionScreen :title="'General Instructions'">
      In the following, there are several images showing two cubes. 
      <br /> 
      By pressing two keys assigned to either 'same' or 'different', you will have to decide whether the cubes are the same or whether they differ.
      <br />
      <br />
      There will be a short training phase where you can get familiar with the task and a main phase.
      <br />
      Please make sure not to distract yourself.
      <br /> 
      Click 'NEXT' to start the experiment.
      <br /> 
      <br /> 
      Have fun!
    </InstructionScreen>

    <!-- Here we create screens in a loop for every entry in a random trial -->
    <template v-for="(training_task, i) of training_trials">
      <KeypressScreen
	:fixationTime=getRandomTime()
        :keys="getRandomKey()"
        :options="[training_task.option1, training_task.option2]"
	question="Are the objects same or different?"
	:responseTime="7500"
	:feedbackTime="800"
      >

        <template #stimulus>
          <img :src="training_task.picture" />
	  <Record :data="{...training_task, 'trial_type': 'training', 'trial_number': i}" />
        </template>
	<button @click="$magpie.saveAndNextScreen()">Submit</button>
	<template #feedback>
		<p v-if="!$magpie.measurements.hasOwnProperty('response')">Unfortunately, you ran out of time. Make your choice faster!</p>
		<p v-else-if="$magpie.measurements.response === training_task.expected">Well done, your answer is correct.</p>
		<p v-else>Sorry, your answer was not correct. Try again!</p>  	
	</template>
      </KeypressScreen>
    </template>

    <InstructionScreen :title="'Pause time'">
      Well done, you finished the practice trial and can now take a break.   
      <br /> 
      If you advance, the main trial will start with the same conditions.
      <br />
      <br />
      Keep in mind that you should try to answer correctly and within the given time frame!
      <br />
      <br /> 
      Good luck!
    </InstructionScreen>

    <!-- Here we create screens in a loop for every entry in a random trial -->
    <template v-for="(main_task, i) of main_trials">
      <KeypressScreen
	:fixationTime=getRandomTime()
        :keys="getRandomKey()"
        :options="[main_task.option1, main_task.option2]"
	question="Are the objects same or different?"
	:responseTime="7500"
	:feedbackTime="800"
      >

        <template #stimulus>
          <img :src="main_task.picture" />
	  <Record :data="{...main_task, 'trial_type': 'main', 'trial_number': i}" />
        </template>
	<button @click="$magpie.saveAndNextScreen()">Submit</button>
	<template #feedback>
		<p v-if="!$magpie.measurements.hasOwnProperty('response')">Unfortunately, you ran out of time. Make your choice faster!</p>
		<p v-else-if="$magpie.measurements.response === main_task.expected">Well done, your answer is correct.</p>
		<p v-else>Sorry, your answer was not correct. Try again!</p>  	
	</template>
      </KeypressScreen>
    </template>    
    

    <PostTestScreen />

    <!-- While developing your experiment, using the DebugResults screen is fine,
      once you're going live, you can use the <SubmitResults> screen to automatically send your experimental data to the server. -->
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import main_trials from '../trials/mr_main_trials.csv';
import training_trials from '../trials/mr_training_trials.csv';
import _ from 'lodash';

// sample keys and map them to the different answers
var same_key = _.sample(['j', 'f']);
var version_1 = {'j': 'same', 'f': 'different'};
var version_2 = {'f': 'same', 'j': 'different'};
var random_key = (same_key === 'j') ? version_1 : version_2;

export default {
  name: 'App',
  data() {
    same_key;
    random_key;
    console.log('same_key: ', same_key);
    console.log('random_key: ', random_key);

    return {
      main_trials: _.shuffle(main_trials),
      training_trials: _.shuffle(training_trials),
      range: _.range
    };
  },
  methods: {
     getRandomTime() {
	 return _.sample([200, 300, 400, 600, 800, 1000, 1200])
     },
     getRandomKey() {
      return random_key 
    },
  },
  mounted() {
     this.$magpie.addExpData({
         same_key : same_key
     });
 }
};



</script>
