<template>
   <div>
      <h3 class="title">Cadastro de Colaboradores</h3>
      <br>
      <div class="box box-default">
         <div class="box-header with-border">
            <h3 class="box-title" style="color: gray;"><i class="fa fa-edit"></i> &nbsp;Cadastro</h3>
         </div>
         <div>
            <p></p><b>&nbsp;&nbsp;(*)</b>&nbsp;Preenchimento obrigatório<p></p>
         </div>
         <form class="form-horizontal">
            <div class="box-body">
               <div class="form-group" v-if="employeeId">
                  <label class="col-sm-2 control-label" for="id">ID</label>
                  <div class="col-sm-2">
                     <input type="text" class="form-control" id="id" name="id" readonly="readonly" v-model="employee.id">
                  </div>
               </div>
               <div class="form-group">
                  <label class="col-sm-2 control-label" for="name">Nome<b class="required-field"> *</b></label>
                  <div class="col-sm-5">
                     <input type="text" class="form-control" id="name" name="name" v-model="employee.name" maxlength="50" ref="name">
                  </div>
               </div>
               <div class="form-group">
                  <label class="col-sm-2 control-label" for="recordNumber">Nº Registro<b class="required-field"> *</b></label>
                  <div class="col-sm-3">
                     <input type="text" class="form-control" id="recordNumber" name="recordNumber" v-model="employee.recordNumber" maxlength="10" v-mask="'##########'">
                  </div>
               </div>
               <div class="form-group">
                  <label class="col-sm-2 control-label" for="costCenter">Centro de Custo</label>
                  <div class="col-sm-3">
                     <input type="text" class="form-control" id="costCenter" name="costCenter" v-model="employee.costCenter" maxlength="20" v-mask="'####################'">
                  </div>
               </div>
               <div class="form-group">
                  <label class="col-sm-2 control-label" for="costHour">Valor hora<b class="required-field"> *</b></label>
                  <div class="col-sm-2">
                     <money class="form-control" id="costHour" name="costHour" v-bind="money" v-model="employee.costHour"></money>
                  </div>
               </div>
               <div class="form-group">
                  <label class="col-sm-2 control-label" for="position">Cargo<b class="required-field"> *</b></label>
                  <div class="col-sm-3">
                     <select class="form-control" name="position" id="position" v-model="employee.position.id">
                        <option :value="position.id" v-for="position in positions" :key="position.id">{{ position.title }}</option>
                     </select>
                  </div>
               </div>
               <div class="form-group">
                  <label class="col-sm-2 control-label" for="company">Empresa<b class="required-field"> *</b></label>
                  <div class="col-sm-5">
                     <select class="form-control" name="company" id="company" v-model="employee.company.id">
                        <option :value="company.id" v-for="company in companies" :key="company.id">{{ company.name }}</option>
                     </select>
                  </div>
               </div>
               <div class="form-group">
                  <label class="col-sm-2 control-label" for="status">Turno<b class="required-field"> *</b></label>
                  <div class="col-sm-3">
                     <select class="form-control" name="officeHour" id="officeHour" v-model="employee.officeHour">
                        <option :value="officeHour.code" v-for="officeHour in officeHours" :key="officeHour.code">{{ officeHour.description }}</option>
                     </select>
                  </div>
               </div>
               <div class="form-group">
                  <label class="col-sm-2 control-label" for="status">Situação<b class="required-field"> *</b></label>
                  <div class="col-sm-2">
                     <select class="form-control" name="status" id="status" v-model="employee.status">
                        <option value="ACTIVE">Ativo</option>
                        <option value="INACTIVE">Inativo</option>
                     </select>
                  </div>
               </div>
            </div>
            <div class="box-footer">
               <div class="col-sm-9 col-sm-offset-2">
                  <button type="submit" class="btn btn-primary" @click.prevent="save"><i class="fa fa-save" aria-hidden="true"></i>&nbsp;Salvar</button>&nbsp;
                  <button type="button" class="btn btn-default" @click.prevent="back"><i class="fa fa-mail-reply" aria-hidden="true"></i>&nbsp;Voltar</button>
               </div>
            </div>
         </form>
      </div>
   </div>
</template>

<script>
import axios from 'axios'
import { baseApiUrl, showError } from '@/global'

export default {
    name: 'employeeForm',
    props: {
          id: String,
    },
    data() {
        return {
            employee: {
               status: 'ACTIVE',
               company: {},
               position: {},
               officeHour: 'HOUR_A',
            },
            employeeId: this.$route.params.id,
            positions: [],
            companies: [],
            officeHours: [],
            money: {
               decimal: ',',
               thousands: '.',
               precision: 2,
               masked: false
            },
        }
    },
    methods: {
      save() {
        const method = this.employee.id ? 'put' : 'post'
        const id = this.employee.id ? `${this.employee.id}` : ''
        const url = `${baseApiUrl}/employees/${id}`
        axios[method](url, this.employee)
          .then(() => {
            this.$toasted.global.defaultSuccess()
            this.$router.push({ path: '/employee' })
          }).catch(showError)
      },
      back() {
        this.$router.push({ path: '/employee' })
      },
      loadEmployee() {
        const url = `${baseApiUrl}/employees/${this.employee.id}`
        axios.get(url)
          .then(res => this.employee = res.data)
          .catch(showError)
      },
      fetchPositions() {
         const url = `${baseApiUrl}/positions`
         axios.get(url)
            .then(response => {
               this.positions = response.data.content
            })
         .catch(showError)
      },
      fetchCompanies() {
         const url = `${baseApiUrl}/companies`
         axios.get(url)
            .then(response => {
               this.companies = response.data.content
            })
         .catch(showError)
      },
      fetchOfficeHours() {
         const url = `${baseApiUrl}/positions/officeHours/SAFRA`
         axios.get(url)
            .then(response => {
               this.officeHours = response.data;
            })
         .catch(showError)
      }
    },
    mounted() {
      if(this.employeeId !== 0) {
        this.employee.id = this.employeeId
        this.loadEmployee()
        this.loadEmployee()
      } 
      this.$refs.name.focus()
      this.fetchPositions()
      this.fetchCompanies()
      this.fetchOfficeHours()
    }
}
</script>

<style scoped>
   input {
      text-transform: uppercase;
   }
</style>
