<template>
  <Table :column="th_list" :entrie="td_list" :columnSort="columnSort" :columnNumber="columnNumber" @update="updateTableData" :status="status">
    <template v-slot:header>
      {{`${t('NSSI_Template')}${t('Template')}`}}
      <!-- Network Slice Subnet Template -->
    </template>
    <template v-slot:button>
      {{`${t('Create')} NSS ${t('Template')}`}}
      <!-- Create NSS Template -->
    </template>
    <template v-slot:table-name>
      {{`NSS ${t('Template')} ${t('list')}`}}
      <!-- NSS Template List -->
    </template>
    <template v-slot:table-td>
      <tr v-for="item in filterEntries" :key="item.templateId">
        <td class="tablecell-custom">{{ item.templateId }}</td>
        <td class="tablecell-custom">{{ item.description }}</td>
        <td class="tablecell-custom">{{ item.nfvoType }}</td>
        <td class="w-0">
          <div class="d-flex justify-content-center align-items-center text-white bg-info rounded-circle cursor-pointer mx-auto" style="width:30px; height:30px" data-bs-toggle="modal" data-bs-target="#show_plugin_Modal" @click="show_template_button(item)">
            <i class="bi bi-file-text-fill"></i>
          </div>
        </td>
        <td class="w-0">
          <div class="d-flex justify-content-center align-items-center text-white bg-primary rounded-circle cursor-pointer mx-auto" style="width:30px; height:30px" @click="allocate_template_button(item)">
            <i class="bi bi-gear-fill"></i>
          </div>
        </td>
        <td class="w-0">
          <div class="d-flex justify-content-center align-items-center text-white bg-danger rounded-circle cursor-pointer mx-auto" style="width:30px; height:30px" data-bs-toggle="modal" data-bs-target="#delete_plugin_Modal" @click="delete_template_button(item)">
            <i class="bi bi-trash"></i>
          </div>
        </td>
      </tr>
    </template>
  </Table>
  <Modalcreate ref="modalCreate" @remove="removeCreateData">
    <template v-slot:header>
      {{`${t('Create')}${t('new')}NSS${t('Template')}`}}
      <!-- Create new NSS Template -->
    </template>
    <template v-slot:body>
      <form>
        <div class="mb-3">
          <label for="select1" class="form-label">
            VNF {{t('Template')}} :
            <!-- VNF Template : -->
          </label>
          <select v-model="currentVNF" class="form-select form-select" :class="{ 'is-invalid' : select_vnf_invalidated }" id="select1" aria-label=".form-select example">
            <option selected>
              {{`${t('Please')}${t('select')} ...`}}
              <!-- 請選擇 ... -->
            </option>
            <option v-for="item in sortTemplateVNFList" :key="item.templateId" :value="item.templateId">{{ item.name }}</option>
          </select>
          <div class="mt-2" v-if="currentVNF != '請選擇 ...'">
            <div>
              VNF {{t('ID')}} : {{ currentVNF }}
              <!-- VNF ID : {{ currentVNF }} -->
            </div>
            <div>
              VNF {{t('Description')}} : {{ currentVNFDescription }}
              <!-- VNF Description : {{ currentVNFDescription }} -->
            </div>
          </div>
          <div class="invalid-feedback">
            {{`${t('Please')}${t('select')}${t('one')}VNF${t('Template')}`}}
            <!-- 請選擇一個 VNF Template -->
          </div>
        </div>
        <div class="mb-3">
          <label for="select2" class="form-label">
            NSD {{t('Template')}} :
            <!-- NSD Template : -->
          </label>
          <select v-model="currentNSD" class="form-select form-select" :class="{ 'is-invalid' : select_nsd_invalidated }" id="select2" aria-label=".form-select example">
            <option selected>
              {{`${t('Please')}${t('select')} ...`}}
              <!-- 請選擇 ... -->
            </option>
            <option v-for="item in sortTemplateNSDList" :key="item.templateId" :value="item.templateId">{{ item.name }}</option>
          </select>
          <div class="mt-2" v-if="currentNSD != '請選擇 ...'">
            <div>
              NSD {{t('ID')}} : {{ currentNSD }}
              <!-- NSD ID : {{ currentNSD }} -->
            </div>
            <div>
              NSD {{t('Description')}} : {{ currentNSDDescription }}
              <!-- NSD Description : {{ currentNSDDescription }} -->
            </div>
          </div>
          <div class="invalid-feedback">
            {{`${t('Please')}${t('select')}${t('one')}NSD${t('Template')}`}}
            <!-- 請選擇一個 NSD Template -->
          </div>
        </div>
        <div class="mb-3">
          <label for="select3" class="form-label">
            NRM {{t('Template')}} :
            <!-- NRM Template : -->
          </label>
          <select v-model="currentNRM" class="form-select form-select" :class="{ 'is-invalid' : select_nrm_invalidated }" id="select3" aria-label=".form-select example">
            <option selected>
              {{`${t('Please')}${t('select')} ...`}}
              <!-- 請選擇 ... -->
            </option>
            <option v-for="item in sortTemplateNRMList" :key="item.templateId" :value="item.templateId">{{ item.name }}</option>
          </select>
          <div class="mt-2" v-if="currentNRM != '請選擇 ...'">
            <div>
              NRM {{t('ID')}} : {{ currentNRM }}
              <!-- NRM ID : {{ currentNRM }} -->
            </div>
            <div>
              NRM {{t('Description')}} : {{ currentNRMDescription }}
              <!-- NRM Description : {{ currentNRMDescription }} -->
            </div>
          </div>
          <div class="invalid-feedback">
            {{`${t('Please')}${t('select')}${t('one')}NRM${t('Template')}`}}
            <!-- 請選擇一個 NRM Template -->
          </div>
        </div>
        <div class="mb-3">
          <label for="InputFile" class="form-label">
            {{`NSS${t('Description')} :`}}
            <!-- NSS Description : -->
          </label>
          <input type="text" class="form-control" id="InputFile" :placeholder="Description" v-model="templateDescription">
        </div>
        <div class="mb-2">
          <label for="select4" class="form-label">
            {{`NFVO${t('Name')} :`}}
            <!-- NFVO Name : -->
          </label>
          <select v-model="currentNFVMANO" class="form-select form-select" :class="{ 'is-invalid' : select_nfvmano_invalidated }" id="select4" aria-label=".form-select example">
            <option selected>
              {{`${t('Please')}${t('select')} ...`}}
              <!-- 請選擇 ... -->
            </option>
            <option v-for="item in sortNFVMANOList" :key="item.name" :value="item.name">{{ item.name }}</option>
          </select>
          <div class="invalid-feedback">
            {{`${t('Please')}${t('select')}${t('one')}NFVO`}}
            <!-- 請選擇一個 NFVO -->
          </div>
        </div>
      </form>
    </template>
    <template v-slot:footer>
      <button type="button" class="btn btn-primary text-white" @click="create_template_modal">
        {{t('Create')}}
        <!-- Create -->
      </button>
    </template>
  </Modalcreate>
  <Modalshow ref="modalShow" @remove="removeShowData">
    <template v-slot:header>
      {{t('Generic')}}{{t('Template')}}{{t('list')}}
      <!-- Generic Template List -->
    </template>
    <template v-slot:body>
      <form>
        <div class="mb-3">
          <label for="InputFile" class="form-label">
            NSS{{t('Template')}}{{t('ID')}} :
            <!-- NSS Template ID : -->
          </label>
          <input type="text" class="form-control" id="InputFile" placeholder="請輸入 Plugin 名稱" v-model="templateId" readonly>
        </div>
        <div>
          <label for="VnfList" class="form-label">
            {{t('Template')}}{{t('ID')}}{{t('list')}} :
            <!-- Template ID List : -->
          </label>
            <ul class="list-group list-group-flush">
              <li class="list-group-item">VNF : {{ templateVNFId }}</li>
              <li class="list-group-item">NSD : {{ templateNSDId }}</li>
              <li class="list-group-item">NRM : {{ templateNRMId }}</li>
            </ul>
        </div>
      </form>
    </template>
  </Modalshow>
  <Modaldelete ref="modalDelete" @delete="delete_template_modal" @remove="removeDeleteData">
    <template v-slot:header>
      {{`${t('Delete')}NSS${t('Template')}`}}
      <!-- Delete NSS Template -->
    </template>
  </Modaldelete>
  <Alert v-show="alertInfo.alertExist" v-bind="alertInfo"></Alert>
</template>
<script>
import { ref } from 'vue';
import { $array } from 'alga-js';
import { Share } from '../assets/js/api';
import { defineAsyncComponent } from 'vue';
import { nss_template } from '../assets/js/api';
import Table from '../components/global/table.vue';
import {useI18n} from 'vue-i18n'
const { PluginList, TemplateList } = Share();
const { nssTemplateList,createNssTemplate,deleteNssTemplate }  = nss_template();
const Alert = defineAsyncComponent(() => import(/* webpackChunkName: "Alert" */ '../components/global/alert.vue'));
const Modalshow = defineAsyncComponent(() => import(/* webpackChunkName: "Modalshow" */ '../components/global/modal-show.vue'));
const Modalcreate = defineAsyncComponent(() => import(/* webpackChunkName: "Modalcreate" */ '../components/global/modal-create.vue'));
const Modaldelete = defineAsyncComponent(() => import(/* webpackChunkName: "Modaldelete" */ '../components/global/modal-delete.vue'));
export default {
  components: {
    Table,
    Alert,
    Modalshow,
    Modalcreate,
    Modaldelete,
  },
  setup() {
    const {t} = useI18n()
    const modalCreate = ref(null)
    const th_list = [
        { name: "templateId", text: t("ID") },
        { name: "description", text: t("Description") },
        { name: "nfvoType", text: t("NFVO") },
        { name: "template_list", text: t("Template") },
        { name: "allocate_nssi", text: t("Allocate") },
        { name: "delete_template", text: t("Delete") },
    ]
    const Description = t('Description')
    return{
      modalCreate,t,th_list,Description
    }
  },
  data() {
    return {
      status: false,
      filterEntries: [],
      // th_list: [
      //   { name: "templateId", text: "ID" },
      //   { name: "description", text: "Description" },
      //   { name: "nfvoType", text: "NFVO" },
      //   { name: "template_list", text: "Template" },
      //   { name: "allocate_nssi", text: "Allocate" },
      //   { name: "delete_template", text: "Delete" },
      // ],
      td_list: [],
      nfv_mano_list: [],
      columnSort: ['templateId','description','nfvoType'],
      columnNumber: 6,
      currentNFVMANO: '請選擇 ...',
      currentVNF: '請選擇 ...',
      currentNSD: '請選擇 ...',
      currentNRM: '請選擇 ...',
      templateId: '',
      templateDescription: '',
      templateData: {},
      select_vnf_invalidated: false,
      select_nsd_invalidated: false,
      select_nrm_invalidated: false,
      select_nfvmano_invalidated: false,
      templateVNFList: [],
      templateNSDList: [],
      templateNRMList: [],
      templateVNFId: '',
      templateNSDId: '',
      templateNRMId: '',
      alertInfo: {
        alertExist: false,
        alertStatus: false,
        alertColor: '',
        alertIcon: '',
        alertTitle: '',
        alertContent: '',
      }
    }
  },
  computed: {
    is_invalidated() { // Create Modal 驗證 
      return this.select_vnf_invalidated || this.select_nsd_invalidated || this.select_nrm_invalidated || this.select_nfvmano_invalidated;
    },
    sortTemplateVNFList() {
      return $array.sortBy(this.templateVNFList, 'name', 'asc');
    },
    sortTemplateNSDList() {
      return $array.sortBy(this.templateNSDList, 'name', 'asc');
    },
    sortTemplateNRMList() {
      return $array.sortBy(this.templateNRMList, 'name', 'asc');
    },
    sortNFVMANOList() {
      return $array.sortBy(this.nfv_mano_list, 'name', 'asc');
    },
    currentVNFDescription() { // Create Modal 內 VNF Description
      const index = this.templateVNFList.findIndex(x => x.templateId == this.currentVNF);
      return this.templateVNFList[index].description;
    },
    currentNSDDescription() { // Create Modal 內 NSD Description
      const index = this.templateNSDList.findIndex(x => x.templateId == this.currentNSD);
      return this.templateNSDList[index].description;
    },
    currentNRMDescription() { // Create Modal 內 NRM Description
      const index = this.templateNRMList.findIndex(x => x.templateId == this.currentNRM);
      return this.templateNRMList[index].description;
    },
  },
  watch: {
    currentVNF: {
      handler: function() {
        this.select_vnf_invalidated = false;
      }
    },
    currentNSD: {
      handler: function() {
        this.select_nsd_invalidated = false;
      }
    },
    currentNRM: {
      handler: function() {
        this.select_nrm_invalidated = false;
      }
    },
    currentNFVMANO: {
      handler: function() {
        this.select_nfvmano_invalidated = false;
      }
    },
  },
  async created() {
    try {
      await this.axios.all([this.getTableData(), this.getNfvManoData(), this.getNssData()]);
    }
    catch(err) {
      console.log(err);
    }
    await this.delay(700);
    this.status = true;
  },
  methods: {
    async getTableData() {  // 獲取 VNF NSD NRM 資料
      let res = await TemplateList();
      for(let i of res.data) {
        if(i.operationStatus == 'UPLOAD'){
          switch(i.templateType) {
            case 'VNF':
              this.templateVNFList.push(i);
              break;
            case 'NSD':
              this.templateNSDList.push(i);
              break;
            case 'NRM':
              this.templateNRMList.push(i);
              break;
            default: 
              console.log('templateType error');
          }
        }
      }
    },
    async getNfvManoData() { // 獲取 NFVMANO 資料
      let res = await PluginList();
      for(let i of res.data) {
        this.nfv_mano_list.push(i);
      }
    },
    async getNssData() { // 獲取 NSS Template 資料
      let res = await nssTemplateList();
      this.td_list = [];
      for(let i of res.data) {
        if(i.genericTemplates.length == 3) {
          let nfvoType = i.nfvoType.length == 0 ? '' : i.nfvoType[0];
          let obj = {
            description: i.description,
            genericTemplates: i.genericTemplates,
            instanceId: i.instanceId,
            templateId: i.templateId,
            nfvoType: nfvoType,
          }
          this.td_list.push(obj);
        }
      }
    },
    delay(interval) { // 計時器
      return new Promise((resolve) => {
        setTimeout(resolve,interval);
      })
    },
    async setAlertData(color,icon,title,content) { // alert 的樣式
      this.alertInfo.alertStatus = false; // 避免重複動作太快
      this.alertInfo.alertExist = false; // 避免重複動作太快
      this.alertInfo.alertColor = color;
      this.alertInfo.alertIcon = icon;
      this.alertInfo.alertTitle = title;
      this.alertInfo.alertContent = content;
      this.alertInfo.alertStatus = true;
      this.alertInfo.alertExist = true;
      await this.delay(1500);
      this.alertInfo.alertStatus = false;
      await this.delay(100);
      this.alertInfo.alertExist = false;
      this.alertInfo.alertColor = '';
      this.alertInfo.alertIcon = '';
      this.alertInfo.alertTitle = '';
      this.alertInfo.alertContent = '';
    },
    updateTableData(val) {  // 每次執行 Table 操作，更新資料 
      this.filterEntries = val;
    },
    removeCreateData() { // 關閉 Create Modal
      this.templateDescription = '';
      this.currentVNF = '請選擇 ...';
      this.currentNSD = '請選擇 ...';
      this.currentNRM = '請選擇 ...';
      this.currentNFVMANO = '請選擇 ...';
      this.select_vnf_invalidated = false;
      this.select_nsd_invalidated = false;
      this.select_nrm_invalidated = false;
      this.select_nfvmano_invalidated = false;
    },
    removeShowData() { // 關閉 Show Modal
      this.templateId = '';
      this.templateVNFId = '';
      this.templateNSDId = '';
      this.templateNRMId = '';
    },
    removeDeleteData() { // 關閉 Delete Modal
      this.templateData = {};
    },
    create_template_validate() { // 驗證 Create Modal
      if(this.currentVNF == '請選擇 ...')
        this.select_vnf_invalidated = true;
      if(this.currentNSD == '請選擇 ...')
        this.select_nsd_invalidated = true;
      if(this.currentNRM == '請選擇 ...')
        this.select_nrm_invalidated = true;
      if(this.currentNFVMANO == '請選擇 ...') 
        this.select_nfvmano_invalidated = true;
    },
    async create_template_modal() { // 點擊 Create Modal 內創建按鈕
      this.create_template_validate();
      if(!this.is_invalidated) {
        let form = new FormData();
        form.append("nfvoType", this.currentNFVMANO);
        form.append("genericTemplates", this.currentVNF);
        form.append("genericTemplates", this.currentNSD);
        form.append("genericTemplates", this.currentNRM);
        form.append("description", this.templateDescription);
        try {
          await createNssTemplate(form);
          await this.getNssData();
          this.setAlertData('alert-success', 'bi bi-check-circle-fill', 'Operates Successfully', 'NSS Template has been created !');
        }
        catch(err) {
          console.log(err);
          this.setAlertData('alert-danger', 'bi bi-x-circle-fill', 'Operates Unsuccessfully', 'Fail to create the NSS Template !');
        }
        this.$refs.modalCreate.closeModalEvent();
      }
    },
    show_template_button(item) { // 點擊 Show Modal 按鈕
      this.templateId = item.templateId;
      const indexVNF = item.genericTemplates.findIndex(x => x.templateType == "VNF");
      const indexNSD = item.genericTemplates.findIndex(x => x.templateType == "NSD");
      const indexNRM = item.genericTemplates.findIndex(x => x.templateType == "NRM");
      this.templateVNFId = item.genericTemplates[indexVNF].templateId;
      this.templateNSDId = item.genericTemplates[indexNSD].templateId;
      this.templateNRMId = item.genericTemplates[indexNRM].templateId;
    },
    allocate_template_button(item) { // 點擊 Allocate Modal 按鈕
      this.$router.push({ path: '/nssi_topology/', query: { id: item.templateId, status: 'allocate'}});
    },
    delete_template_button(file) { // 點擊 Delete Modal 按鈕
      this.templateData = file;
    },
    async delete_template_modal() { // 點擊 Delete Modal 內刪除按鈕
      try {
        await deleteNssTemplate(this.templateData.templateId);
        await this.getNssData();
        this.setAlertData('alert-success', 'bi bi-check-circle-fill', 'Operates Successfully', 'NSS Template has been deleted !');
      }
      catch(err) {
        console.log(err);
        this.setAlertData('alert-danger', 'bi bi-x-circle-fill', 'Operates Unsuccessfully', 'Fail to delete the NSS Template !');
      }
    },
  }
}
</script>