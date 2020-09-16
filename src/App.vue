
<template>
  <div id="app" class="root-tirada-pedido">
    <contact-form v-bind:form-data=formData></contact-form>
    <produto-do-pedido v-bind:product-arr=productArr v-bind:file-list=fileList></produto-do-pedido>
  </div>
</template>

<script>

import "bootstrap/dist/css/bootstrap.min.css"
import "bootstrap-vue/dist/bootstrap-vue.css"

import ProdutoDoPedido from "./components/produtoDoPedido";
import ContactForm from "@/components/contactForm";
// let sweetalert2 = require("sweetalert2")
import Swal from "sweetalert2"
import {cpf, cnpj} from "cpf-cnpj-validator"
import BackendCaller from "@/components/BackendCaller";

export default {
  name: "App",
  computed: {
    fullAddress: function(){
      return this.formData.address + (this.formData.neighbourhood === "" ? "" :  ", " + this.formData.neighbourhood)
    },
    objToSave: function(){
      let contactNameSplited = this.formData.contactName.split(" ");
      return {
            contact: {
              FirstName: contactNameSplited.slice(0, contactNameSplited.length - 1).join(" "),
              LastName: contactNameSplited[contactNameSplited.length - 1],
              Email: this.formData.email,
              Phone: this.formData.phone
            },
            opportunity: {},
            account: {
              CPFCNPJ__c: this.formData.tipoPessoa === "pj" ? this.formData.cnpj : this.formData.cpf,
              Name: this.formData.companyName || this.formData.contactName ,
              InscricaoEstadual__c: this.formData.companyIe,
              BillingStreet: this.fullAddress,
              BillingCity: this.formData.city,
              BillingState: this.formData.state,
              BillingPostalCode: this.formData.postalCode,
              BillingCountry: this.formData.country,
              ShippingStreet: this.fullAddress,
              ShippingCity: this.formData.city,
              ShippingState: this.formData.state,
              ShippingPostalCode: this.formData.postalCode,
              ShippingCountry: this.formData.country,
            },
            produtoOportunidadePortalList: this.productArr.map(item => {
              return {
                Produto__c: item.name,
                Quantidade__c : item.quantity
              }
            })
        }
    }
  },
  data: function(){
      return {
        backendCaller: BackendCaller,
        fileList: [], // Store our uploaded files
        formData: {
          validation: {
            tipoPessoa: "",
            cpf: "",
            cnpj: "",
            companyName: "",
            companyIe: "",
            contactName: "",
            phone: "",
            email: "",
            postalCode: "",
            address: "",
            neighbourhood: "",
            city: "",
            state: "",
            country: "",
          },
          validationMainMessage: "",
          tipoPessoa: "pf",
          cpf: "",
          cnpj: "",
          companyName: "",
          companyIe: "",
          contactName: "",
          phone: "",
          email: "",
          postalCode: "",
          address: "",
          neighbourhood: "",
          city: "",
          state: "",
          country: "Brasil",
        },
        productArr: [{
          name: "",
          quantity: 1,
          validation: ""
        }],
        name: ""
      }
    },

  components: {
    ContactForm,
    ProdutoDoPedido
  },
  methods: {
    isFormValid: function(){
      console.log(this)
      this.formData.validationMainMessage = ""
      Object.keys(this.formData).forEach(field => {
        this.formData.validation[field] = ""
      })
      if (this.formData.tipoPessoa === "pf"){
        if (!this.formData.cpf.trim()) this.formData.validation.cpf = "Campo é obrigatório"
        if (!cpf.isValid(this.formData.cpf)) this.formData.validation.cpf = "CPF inválido"
      }else{
        this.formData.validation.cpf = ""
      }
      if (this.formData.tipoPessoa === "pj"){
        if (!this.formData.cnpj.trim()) this.formData.validation.cnpj = "Campo é obrigatório"
        if (!this.formData.companyName.trim()) this.formData.validation.companyName = "Campo é obrigatório"
        if (!this.formData.companyIe.trim()) this.formData.validation.companyIe = "Campo é obrigatório"
        if (!cnpj.isValid(this.formData.cnpj)) this.formData.validation.cnpj = "CNPJ inválido"

        if (this.formData.companyIe.trim().length > 80) this.formData.validation.contactName = "Valor muito longo"
        if (this.formData.companyName.trim().length > 80) this.formData.validation.contactName = "Valor muito longo"
      }else{
        this.formData.validation.cnpj = ""
        this.formData.validation.companyName = ""
        this.formData.validation.companyIe = ""
      }

      if (!this.formData.contactName.trim()) this.formData.validation.contactName = "Campo é obrigatório"
      if (!this.formData.tipoPessoa.trim()) this.formData.validation.tipoPessoa = "Campo é obrigatório"
      if (!this.formData.phone.trim()) this.formData.validation.phone = "Campo é obrigatório"
      if (!this.formData.email.trim()) this.formData.validation.email = "Campo é obrigatório"
      if (!this.formData.postalCode.trim()) this.formData.validation.postalCode = "Campo é obrigatório"
      if (!this.formData.address.trim()) this.formData.validation.address = "Campo é obrigatório"
      if (!this.formData.neighbourhood.trim()) this.formData.validation.neighbourhood = "Campo é obrigatório"
      if (!this.formData.city.trim()) this.formData.validation.city = "Campo é obrigatório"
      if (!this.formData.state.trim()) this.formData.validation.state = "Campo é obrigatório"
      if (!this.formData.country.trim()) this.formData.validation.country = "Campo é obrigatório"
      console.log(this.formData.contactName.trim().length)
      if (this.formData.contactName.trim().length > 40) this.formData.validation.contactName = "Valor muito longo"
      if (this.formData.tipoPessoa.trim().length > 40) this.formData.validation.tipoPessoa = "Valor muito longo"
      if (this.formData.phone.trim().length > 20) this.formData.validation.phone = "Valor muito longo"
      if (this.formData.email.trim().length > 40) this.formData.validation.email = "Valor muito longo"
      if (this.formData.postalCode.trim().length > 10) this.formData.validation.postalCode = "Valor muito longo"
      if (this.formData.address.trim().length > 50) this.formData.validation.address = "Valor muito longo"
      if (this.formData.neighbourhood.trim().length > 50) this.formData.validation.neighbourhood = "Valor muito longo"
      if (this.formData.city.trim().length > 50) this.formData.validation.city = "Valor muito longo"
      if (this.formData.state.trim().length > 50) this.formData.validation.state = "Valor muito longo"
      if (this.formData.country.trim().length > 40) this.formData.validation.country = "Valor muito longo"

      if (!this.validateEmail(this.formData.email)) this.formData.validation.email = "Email inválido"

      Object.values(this.formData.validation).filter((validation) => {
        if (validation){
          this.formData.validationMainMessage = "Verifique os dados de contato"
        }
      })

      if (this.formData.validationMainMessage){
        Swal.fire({icon: "error", title: "Oops...", text: this.formData.validationMainMessage, })
        return false
      }

      this.productArr.forEach(item => {
        item.validation = ""
        if (item.name === ""){
          item.validation = "Campo é obrigatório"
        }
        if (item.quantity <= 0){
          item.validation = "Quantidade não pode ser zero"
        }
      })
      console.log(this.productArr.filter(item => item.validation !== ""))
      if (this.productArr.filter(item => item.validation !== "").length > 0){
        Swal.fire({icon: "error", title: "Oops...", text: "Revise os dados do pedido!", })
        return false
      }
      return true
    },
    validateEmail: function(email){
      const re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(String(email).toLowerCase());
    },
    saveOrder: function () {
        try {
          Swal.showLoading()
          if (!this.isFormValid()){
            return false
          }
          BackendCaller.methods.saveOrder(this.objToSave)
          .then(resultObject => {
            console.log('resultObject', resultObject)
            BackendCaller.methods.saveAttachments(this.fileList, resultObject.Id).then(resultAttachment => {
              console.log('resultAttachment', resultAttachment)
              // Swal.fire({icon: 'success', title: 'Tudo certo!', text: 'Recebemos seu pedido e logo entraremos em contato!', })
              Swal.fire({icon: 'success', title: 'Tudo certo!', text: `Esse é o código do seu pedido:  ${resultObject.CodigoOportunidade__c}`})
            })
          }).catch(responseError => {
            console.log('responseError', responseError)
          })
        }catch (e){
          console.error(e)
          Swal.close()
        }
      }
  }
}

</script>

<style>
  @keyframes fade-in-scale {
    from {transform: scale(0);}
    to {transform: scale(1);}
  }
  body{
    background-color: lightgrey !important;
  }
  .root-tirada-pedido{
    max-width: 23cm;
    margin: 0 auto;
    /*background-color: #eaeaea !important;*/
    display: flow-root;
  }
</style>