<template>
  <div class="hello container">

    <b-form>
      <b-form-group
        id="employeeNameGroup"
        label-cols-sm="4"
        label-cols-lg="3"
        label="Employee Name"
        label-for="employee-name"
        label-size="sm"
      >
        <b-form-input id="employee-name" v-model="employeeName" type="text" placeholder="Enter employee name" />
      </b-form-group>

      <b-form-group
        id="dependentGroup"
        label-cols-sm="4"
        label-cols-lg="3"
        label="Dependents"
        label-size="sm"
      >
        <div v-for="d in dependents">
          <b-form-input class="dependent" type="text" v-model="d.value" placeholder="Add dependent name" />
        </div>
      </b-form-group>

      <b-button id="add-dependent" v-on:click="addDependent" variant="outline-primary" :aria-disabled="calculated">Add Dependent</b-button>
    </b-form>

    <div class="button-container" >
      <b-button id="calculate" v-if="!calculated" v-on:click="calculate" variant="success">Calculate</b-button>
      <b-button id="reset" v-if="calculated" v-on:click="reset" variant="success">Reset</b-button>
    </div>

    <div class="result-container" v-if="calculated">
      {{employeeName}}'s salary is ${{salary}}. Total deductions are ${{deductions}} with {{dependents.filter(x => x.value).length}} dependents.
    </div>
  </div>

</template>

<script>
export default {
  name: "HelloWorld",
  data: function() {
    return {
      deductions: 0,
      paychecks: 26,
      employeeName: "",
      salary: 0,
      pay: 2000,
      calculated: false,
      dependents: [{ value: "" }]
    };
  },
  methods: {
    addDependent: function() {
      if (this.dependents.length == 5) {
        return;
      }
      this.dependents.push({ value: "" });
    },
    reset: function(){
      this.calculated = false;
      this.employeeName = "";
      this.deductions = 0;
      this.salary = 0;
      this.dependents = [{ value: "" }];
    },
    calculate: function() {

      if(!this.employeeName){
        return;
      }

      //salary is 26 paychecks times pay
      this.salary = this.paychecks * this.pay;

      //start out with initial deductions for the year
      this.deductions = 1000;

      //reduce deductions if name starts with a
      if (this.employeeName.toLowerCase().startsWith("a")) {
        this.deductions -= 10 / 100 * 1000;
      }

      if (this.dependents) {
        this.dependents.forEach(dependent => {
          //check if value has text and passes regular expression
          if(dependent.value && /^[a-z ,.'-]+$/i.test(dependent.value)){

            //add deduction
            this.deductions += 500;

            //reduce deductions if name starts with a
            if (dependent.value.toLowerCase().startsWith("a")) {
              this.deductions -= 10 / 100 * 500;
            }
          }
        });
      }

      //subtract all deductions
      this.salary -= this.deductions;

      this.calculated = true;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.container {
  max-width: 550px;
  border: 1px solid black;
  height: 550px;
  border-radius: 15px;
  position: relative;
}

#employeeNameGroup {
  margin-top: 15px;
}

#add-dependent {
  float: right;
}

#calculate, #reset {
  position: absolute;
  bottom: 0;
  margin-bottom: 15px;
}

#result-container{
  position: absolute;
  bottom: 0;
  margin-bottom: 15px;
}

.dependent {
  margin-bottom: 5px;
}
</style>
