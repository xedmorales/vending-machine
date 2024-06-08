<template>
    <div class="container">
      <div class="vending-machine">
        <img :src="iconPath" alt="Vending Machine Icon" />
        <div class="title-text">VENDING MACHINE</div>
        <div class="subheader-text">(Philippine Currency Edition)</div>
        <div class="desc-text"> Note: This app dispenses the specific values of<br>"Change" based on Philippine Currency</div>
      </div>
      <div class="item-form" v-if="noChange">
        <div class="form-group">
          <label class="input-label"> BILL</label><br>
          <input type="number" class="form-control" v-model="billValue" id="billValue"><br>
        </div>
        <div class="form-group">
          <label class="input-label"> OWED AMOUNT </label><br>
          <input type="number" class="form-control" v-model="owedValue" id="owedValue"><br>
          <button class="btn-primary" @click = "getChange(billValue,owedValue)"> Get Change and Denomination Counterpart </button>
        </div>
        <div class="desc-text warning" v-if="invalidValues">Invalid value for Bill and Owed Amounts</div>
      </div>
      <div class="item-form" v-else>
        <div v-if="changeBreakdown" class="change-breakdown">
          <div class="subheader-text amount-holder"><h5 class="bold-weight">Amount Receivable : PHP {{ storedChange }}</h5></div>
          <div class="subheader-text ptop">Change Breakdown</div>
          <ul>
            <li v-for="change in changeBreakdown" :key="denomination">
                <div class="denomination-handler">
                  {{ change.denomination }} x ₱{{ change.amount }} {{ change.type }}
                </div>
            </li>
          </ul>
          <div class="desc-text warning" v-if="zeroValue">No change Breakdown since amount is PHP 0</div>
        </div>
        <button class="btn-secondary" @click = "resetValues"> Return to Computation Area </button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'App',
    data(){
      return{
        iconPath : require('@/assets/images/icon.png'),
        iconAlt : "Vending Machine Icon",
        owedValue: 0,
        billValue: 0,
        changeBreakdown: null,
        storedChange :0,
        noChange:true,
        invalidValues : false,
        zeroValue : false
      }
    },
    methods:{
      getChange(bill, owed){
        const denominations = [1000, 500, 200, 100, 50, 20, 10, 5, 1];
        let change = bill - owed;
        this.storedChange = change;
        let changeBreakdown = [];
  
        this.invalidValues = owed < 0 || owed > 1000 || bill < owed ? true : false;
        this.zeroValue = this.storedChange == 0 ? true : false;
  
        if(!this.invalidValues){
  
          for (let i = 0; i < denominations.length; i++) {
            // const denomination = denominations[i];
            const numBills = Math.floor(change / denominations[i]);
            if (numBills > 0) {
              const breakdownItem = {
                denomination: numBills, // Number of bills or coins
                amount: denominations[i], // Denomination amount
                type: denominations[i] > 10 ? "Bill" : "Coin" // Determine if it's a bill or coin
              };
              // Push the new object into the changeBreakdown array
              changeBreakdown.push(breakdownItem);
              change -= numBills * denominations[i];
            }
          }
          this.changeBreakdown = changeBreakdown;
          this.noChange = !this.noChange;
        }
      },
      resetValues(){
        this.noChange = !this.noChange;
        this.owedValue = 0;
        this.billValue = 0;
      },
    }
  }
  </script>
  
  <style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap');
  #app {
    font-family: "Montserrat", sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    
  }
  
  /** element holder */
  .container{
    display: flex !important;
    align-items: center;
    align-content: center;
    flex-wrap: wrap;
    justify-content: space-around;
    justify-items: center;
    margin: 0;
    padding: 0;
    height: 100% !important;
  }
  
  h4{
    font-weight: normal;
  }
  .title-text{
    font-size: 40px !important;
    font-weight: 600;
  }
  .subheader-text{
    font-size: 20px !important;
    font-weight: 400;
  }
  .icon-file{
    width: 250px !important
  }
  .vending-machine {
    text-align: center;
    margin-top: 200px !important;
  }
  .vending-machine img {
    max-width: 50%;
    height: auto;
  }
  
  .title-text, .subheader-text {
    display: block;
    margin-top: 30px; /* Adjust as needed for spacing */
  }
  .title-text {
    font-size: 24px;
    font-weight: bolder;
    color:#434343;
  }
  .subheader-text {
    margin-top: 10px !important;
    font-size: 20px;
    font-weight:500;
    color: #2babd6;
  }
  .desc-text {
    margin-top: 20px !important;
    font-size: 16px;
    font-weight: 400;
    color: gray;
  }
  
  /** holder of input and buttons */
  .item-form{
    margin-top:120px;
    padding:70px !important;
    background-color: #fbfbfb;
    border-radius: 5px !important;
    width:700px !important;
    box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px, rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
    height:500px;
  }
  
  /** classes for inputs and buttons  */
  .form-control{
    margin-top: 10px;
    width: 650px !important;
    padding-block: 0 !important;
    padding-inline: 0 !important;
    padding: 20px !important;
    padding-right:20px !important;
    border: solid 1px #e3e3e3;
    border-radius: 5px !important;
    font-family: "Montserrat", sans-serif;
  }
  .form-control:hover{
    border: 1px solid #279ac1;
  }
  .form-group {
    display: flex;
    flex-direction: column;
    align-items:flex-start;
    gap: 30;
  }
  
  .btn-primary{
    padding: 20px !important;
    background-color: #279ac1;
    color: white;
    max-width: 100% !important;
    width: 700px !important;
    margin-top:20px !important;
    font-family: "Montserrat", sans-serif;
    font-size: 15px !important;
    border: 1px solid #279ac1;
    border-radius: 5px !important;
    cursor: pointer;
  }
  
  .btn-primary:hover{
    background-color: #176d8b;
    border: 1px solid #176d8b;
  }
  
  .btn-secondary{
    padding: 20px !important;
    background-color: #c3c3c3;
    color: white;
    max-width: 100% !important;
    width: 700px !important;
    margin-top:20px !important;
    font-family: "Montserrat", sans-serif;
    font-size: 15px !important;
    border: 1px solid #c3c3c3;
    border-radius: 5px !important;
    cursor: pointer;
  }
  
  .btn-secondary:hover{
    background-color: #a9a9a9;
    border: 1px solid #a9a9a9;
  }
  .input-label{
    font-weight: 600;
    margin-top:20px !important;
    font-size: 15px !important;
  }
  .warning{
    color:red !important;
  }
  .amount-holder{
    background-color: #279ac1;
    padding:10px !important;
    color: white;
    border-radius: 5px !important;
    box-shadow: rgba(33, 35, 38, 0.1) 0px 10px 10px -10px;
  }
  .bold-weight{
    font-weight: 600;
  }
  .ptop{
    padding-top: 20px !important;
  }
  
  ul{
    list-style-type: none;
    text-align: center;
    padding-inline-start: 0 !important;
  }
  .denomination-handler{
    background-color: #e9e9e9;
    box-shadow: rgba(33, 35, 38, 0.1) 0px 10px 10px -10px;
    padding:10px;
    border-radius: 5px !important;
    margin: 5px;
  }
  </style>
  