<template>
  <BasicModal
    width="900px"
    v-bind="$attrs"
    @ok="handleOk()"
    @cancel="handleClose()"
    @visible-change="openOrClose"
    @register="register"
    :loading="modelLoadIng"
    :canFullscreen="false"
    :maskClosable="false"
    :draggable="false"
    :footer="null"
    loadingTip="查询参数初始化中"
  >
    <template #title>
      <div style="height: 30px;width: 100%;background-color: #5f375c;color: white;line-height: 30px;">
        <AppstoreOutlined  style="margin: 0 2px;font-size: 14px;"/>
        <span style="font-size: 14px">销售管理</span>
      </div>
    </template>
    <div class="nc-open-content" style="height: 100%">
      <div class="open-content-up">
        <div style="width: calc(100% - 150px);height: 100%;">
        <div style="display: block;text-align: center;">
          <SearchOutlined style="font-size: 26px;color: #0096c7;"/>
          <span style="font-size: 30px;color: #0096c7;font-weight: bold;">&ensp;销货单列表</span>
        </div>
        <ul>
          <li>
            <div class="special-border-div">
              <span>业务范围</span>
            <AccountPicker theme="three" @reloadTable="codeChange"/>
            </div>
          </li>
          <li>
            <div class="special-border-div">
              <span>查询日期</span>
              <div>
                <div class="sbd-left">
                  <a-radio-group style="width: 100%" v-model:value="radiovalue"
                                 @change="radiochange">
                    <p>
                      <a-radio value="1"></a-radio>
                      <span>会计期间：</span>
                      <a-select
                        v-model:value="strDate"
                        show-search
                        :disabled="dateselflg"
                        style="width: 94px;"
                        @focus="focusStrDate"
                        @change="handleChangeStrDate"
                      >
                        <a-select-option
                          v-for="item in strDateList"
                          :value="item.stockYear+''+item.stockMonth"
                        >
                          {{ item.stockYear+''+item.stockMonth }}
                        </a-select-option>
                      </a-select>
                      <span>&emsp;~&emsp;</span>
                      <a-select
                        v-model:value="endDate"
                        show-search
                        :disabled="dateselflg"
                        style="width: 94px"
                        @focus="focusEndDate"
                        @change="handleChangeEndDate"
                      >
                        <a-select-option
                          v-for="item in endDateList"
                          :value="item.stockYear+''+item.stockMonth"
                        >
                          {{ item.stockYear+''+item.stockMonth }}
                        </a-select-option>
                      </a-select>
                    </p>
                    <p>
                      <a-radio value="2"></a-radio>
                      <span>单据日期：</span>
                        <a-date-picker v-model:value="riqi[0]"   :disabled="timeselflg" style="width: 114px;"  format="YYYY-MM-DD" />      <span>~</span>
                        <a-date-picker v-model:value="riqi[1]"   :disabled="timeselflg" style="width: 114px;"  format="YYYY-MM-DD" />
<!--                      <a-range-picker
                        v-model:value="riqi"
                        style="width: 224px;"
                        :disabled="timeselflg"
                        @change="timechange"
                      />-->
                    </p>
                  </a-radio-group>
                </div>
                <div class="sbd-right">
                  <a-radio-group size="small" :disabled="radiovalue=='2'" @change="checkChange">
                    <a-radio-button value="1">上月</a-radio-button>
                    <a-radio-button value="2">本月</a-radio-button>
                    <a-radio-button value="3">本季</a-radio-button>
                    <a-radio-button value="4">本年</a-radio-button>
                  </a-radio-group>
                  <a-radio-group size="small" :disabled="radiovalue=='1'" @change="checkChange">
                    <a-radio-button value="5">今天</a-radio-button>
                    <a-radio-button value="6">昨天</a-radio-button>
                    <a-radio-button value="7">前天</a-radio-button>
                    <a-radio-button value="8">月末</a-radio-button>
                  </a-radio-group>
                </div>

              </div>
            </div>
          </li>
          <li>
            <div class="special-border-div">
            <span>查询条件</span>
            <div style="display: inline-block;width: 100%;">
              <div style="width: 50%;float: left;">
                <span class="right_span">单据编号：</span>
                <a-input :allowClear="true" placeholder="" style="width: 80px"
                         v-model:value="queryModel.variable.ccodeMin"/>
                <span>&emsp;~&emsp;</span>
                <a-input :allowClear="true" placeholder="" style="width: 80px"
                         v-model:value="queryModel.variable.ccodeMax"/>
              </div>
              <div style="width: 50%;float: left;">
                <span class="right_span">单据显示：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  v-model:value="queryModel.constant.queryType">
                  <a-select-option value="1">明细模式</a-select-option>
                  <a-select-option value="2">汇总模式</a-select-option>
                </a-select>
              </div>
              <div style="width: 50%;float: left;">
                <span class="right_span">客户：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  v-model:value="queryModel.variable.cvencode"
                  :options="dataMap['cust']"
                >
                </a-select>
                <PaperClipOutlined style="color: #0a84ff;margin-left: 5px;cursor: pointer;" @click="openHeadSelectContent('cvencode')"/>
              </div>
              <div style="width: 50%;float: left;">
                <span class="right_span">单据状态：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  v-model:value="queryModel.variable.bcheck"
                >
                  <a-select-option value="">全部</a-select-option>
                  <a-select-option value="1">已审核</a-select-option>
                  <a-select-option value="0">未审核</a-select-option>
                </a-select>
              </div>
              <div style="width: 50%;float: left;">
                <span class="right_span">存货：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  :options="dataMap['stock']"
                  v-model:value="queryModel.variable.cinvode"
                >
                </a-select>
                <PaperClipOutlined style="color: #0a84ff;margin-left: 5px;cursor: pointer;" @click="openHeadSelectContent('cinvode')"/>
              </div>
              <div style="width: 50%;float: left;">
                <span class="right_span">存货分类：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  :options="dataMap['whClass']"
                  v-model:value="queryModel.variable.cinvodeClass"
                >
                </a-select>
                <PaperClipOutlined style="color: #0a84ff;margin-left: 5px;cursor: pointer;" @click="openHeadSelectContent('cinvodeClass')"/>
              </div>
              <div style="width: 50%;float: left;" >
                <span class="right_span">仓库：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  :options="dataMap['warehouse']"
                  v-model:value="queryModel.variable.cwhcode"
                >
                </a-select>
                <PaperClipOutlined style="color: #0a84ff;margin-left: 5px;cursor: pointer;" @click="openHeadSelectContent('cwhcode')"/>
              </div>
              <div style="width: 50%;float: left;" >
                <span class="right_span">业务员：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  :options="dataMap['user']"
                  v-model:value="queryModel.variable.cpersoncode"
                >
                </a-select>
                <PaperClipOutlined style="color: #0a84ff;margin-left: 5px;cursor: pointer;" @click="openHeadSelectContent('cpersoncode')"/>
              </div>
              <div style="width: 50%;float: left;" >
                <span class="right_span">业务部门：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  :options="dataMap['dept']"
                  v-model:value="queryModel.variable.cdepcode"
                >
                </a-select>
                <PaperClipOutlined style="color: #0a84ff;margin-left: 5px;cursor: pointer;" @click="openHeadSelectContent('cdepcode')"/>
              </div>
              <div style="width: 50%;float: left;" >
                <span class="right_span">项目：</span>
                <a-select
                  :allowClear="true"
                  show-searchplaceholder=""
                  style="width: 180px"
                  v-model:value="queryModel.variable.citemcode"
                >
                </a-select>
              </div>
            </div>
          </div>
          </li>
        </ul>
        </div>
        <div class="right-btns">
          <Button style="width: 100px;" shape="round" @click="handleOk"  type="primary">查询</Button>
          <Button style="width: 100px;margin-top: 10px" shape="round" @click="handleClose">取消</Button>
        </div>
      </div>
      <ClassAllModalPop @throwData="modalData" @register="registerClassAllPage" />
      <SupperModalPop @throwData="modalData" @register="registerModalPopPage"/>
      <DeptModalPop @register="registerSelectDeptPage" @save="modalData"/>
      <StockCangKuModalPop @register="registerStockCangKuModalPage" @throwData="modalData"/>
      <SelectPsn @register="registerSelectPsnPage" @save="modalData"/>
      <StockInfiModalPop @register="registerStockInfoModalPage" @throwData="modalData"/>
    </div>
    <template #footer>
      <div style="height: 35px">
        <div style="float: left">
<!--          <a-popconfirm
            ok-text="确定"
            cancel-text="关闭"
            @confirm="confirm"
            @cancel="cancel"
          >
            <template #icon><b>过滤条件设置</b></template>
            <template #title>
              <br/>
              <a-transfer
                show-search
                :data-source="filterDatas"
                :titles="['待选条件', '已选条件']"
                :operations="['确定', '取消']"
                :selected-keys="selectedKeys"
                :target-keys="targetKeys"
                :render="item => item.title"
                @change="transferHandleChange"
                @selectChange="transferHandleSelectChange"
              />
            </template>
            <a-button><span style="font-weight: bold;">滤设</span></a-button>
          </a-popconfirm>-->
        </div>
        <div style="float: right" class="right-btns">
          <a-button @click="handleClose" shape="round" style="width: 100px">取消</a-button>
          <a-button @click="handleOk" v-if="!modelLoadIng" type="primary" shape="round"  style="width: 100px">查询</a-button>
        </div>
      </div>
    </template>
  </BasicModal>
</template>

<script setup="props, {content}" lang="ts">
import {computed, onMounted, reactive, ref, unref, watch} from 'vue'
import {BasicModal, useModal, useModalInner} from '/@/components/Modal'
import {
  Checkbox as ACheckbox,
  DatePicker as ADatePicker,
  Input as AInput,
  message,
  Popconfirm as APopconfirm,
  Radio as ARadio,
  Select as ASelect,
  Tabs as ATabs,
  Transfer as ATransfer,Button
} from "ant-design-vue"
import { PaperClipOutlined, SearchOutlined,AppstoreOutlined} from '@ant-design/icons-vue';
import {useTabs} from '/@/hooks/web/useTabs';
import router from "/@/router";
import moment, {Moment} from 'moment';
import {useCompanyOperateStoreWidthOut} from "/@/store/modules/operate-company";
import {useUserStore} from "/@/store/modules/user";
import {hasBlank, pointMessage} from "/@/api/task-api/tast-bus-api";
import {useRouteApi} from "/@/utils/boozsoft/datasource/datasourceUtil";
import {useMessage} from "/@/hooks/web/useMessage";
import {findParameterValue, modifyParameterValue} from "/@/api/task-api/query-bus-api";
import {ObjTool, StrTool} from "/@/api/task-api/tools/universal-tools";
import AccountPicker from "/@/boozsoft/components/AccountPicker/AccountPicker-STOCK.vue";
import dayjs from "dayjs";
import {findStockCaiGouList} from "/@/api/record/stock/stock-caigou";
import ClassAllModalPop from "/@/views/boozsoft/stock/stock_class/popup/classAllModalPop.vue";
import SupperModalPop from "/@/views/boozsoft/global/popup/customer_info/modalPop.vue";
import DeptModalPop from "/@/views/boozsoft/global/popup/dept/select-dept.vue";
import StockCangKuModalPop from "/@/views/boozsoft/stock/stock_cangku/popup/stockCangKuModalPop.vue";
import SelectPsn from "/@/views/boozsoft/global/popup/dept/select-psn.vue";
import StockInfiModalPop from "/@/views/boozsoft/stock/stock_info/popup/stockInfoModalPop.vue";
import {periodAllList} from "/@/api/record/group/im-unit";
const {closeCurrent} = useTabs(router);
const emit = defineEmits(['register', 'query'])

const ARangePicker = ADatePicker.RangePicker
const ASelectOption = ASelect.Option
const AInputSearch = AInput.Search
const ARadioGroup = ARadio.Group
const ARadioButton = ARadio.Button
const ACheckboxGroup = ACheckbox.Group
const ATabPane = ATabs.TabPane

const userStore = useUserStore();
const formItems: any = ref({})
let changeBeforeModel: any = {}
// 会计区间
const dateList: any = ref([])
const quarterList: any = ref([])
// 人员
const dataMap: any = ref({})
// 过滤

const endDate = ref<String>("")
const strDate = ref<String>("")
let endDateList: any = ref([])
let strDateList: any = ref([])
let time: any = ref<Moment[]>([]);
// 日期是否可选
let timeselflg: any = ref(true);
// 期间是否可选
let dateselflg: any = ref(false);
const minKm = ref<string>()
const maxKm = ref<string>()

const busDate = useCompanyOperateStoreWidthOut().getLoginDate
const maxPingzhengQj = ref('')
const ifCrossYear = ref('1')

const userId = userStore.getUserInfo.id
//查询条件
// 已授权账套列表
const accAuthList = ref([])

const currentDynamicTenant = ref('')
const dynamicTenant = ref({})
const {createWarningModal} = useMessage();

const targetKeys: any = ref([])
const selectedKeys: any = ref([])
const filterDatas = [
  {
    key: 'batchId',
    title: `批号`
  }
  , {
    key: 'dvdate',
    title: `失效日期`
  },
  {
    key: 'cwhcode',
    title: `仓库`
  },
  {
    key: 'cpersoncode',
    title: `业务员`
  },
  {
    key: 'cdepcode',
    title: `业务部门`
  },
  {
    key: 'citemcode',
    title: `项目`
  }
]
onMounted(async () => {
});



const [register, {closeModal, setModalProps}] = useModalInner((data) => {
  formItems.value = data.data
  changeBeforeModel = JSON.parse(JSON.stringify(formItems.value))
  setModalProps({minHeight: 500});
})


let count = 0
const codeChange = async (value) => {
  ++count
  if (count == 2) return false; // 防止重复打开
  // 赋值默认参数
  dynamicTenant.value = value
  dbQueryJson.accountId = value.accId;
  defaultModel.constant.tenantId = value.accountMode
  defaultModel.constant.name = value.companyName
  defaultModel.variable.xsShXkd = value.target.xsShXkd
  modelLoadIng.value = true
  let dbQueryData = await findParameterValue(dbQueryJson)
  if (null != dbQueryData) { // 存在
    dbQueryJson.id = dbQueryData.id
    dbQueryJson.queryConditions = dbQueryData.queryConditions
    dbQueryJson.filterConditions = dbQueryData.filterConditions
    if (!hasBlank(dbQueryData.filterConditions)) {
      targetKeys.value = JSON.parse(dbQueryData.filterConditions)['filter1']
    }
    if (!hasBlank(dbQueryData.queryConditions)) {
      let queryMap = JSON.parse(dbQueryData.queryConditions)
      queryModel.constant = queryMap.constant
      queryModel.variable = queryMap.variable
    }
  } else {
    // 清除条件
    queryModel.constant = defaultModel.constant
    queryModel.variable = defaultModel.variable
    dbQueryJson.id = null
  }
  // 初始化基础参数
  queryModel.constant.tenantId = value.accountMode
  currentDynamicTenant.value = value.accountMode
  await reLifeQueryParameter(currentDynamicTenant.value,value.target.id)
  await reLifeEtcList(currentDynamicTenant.value)
  modelLoadIng.value = false
}

const reLifeQueryParameter = async (dynamicTenant,id) => {
  // 加载DB所有会计期间与季报数据
  dateList.value = await periodAllList({id: id});
  if (dateList.value.length > 0) {
    const busDates = busDate.split('-');
    // 查看当前业务日期与期间列表年度是否匹配
    let blist = dateList.value.filter(it => it.stockYear == busDates[0])
    if (blist.length > 0) { // 存在与业务日期相匹配的年度 使用业务期间 反之使用最大年度期间
      let busQj = blist.filter(it => it.stockMonth == (busDates[1]))[0]
      maxPingzhengQj.value = (busQj.stockYear+''+busQj.stockMonth)
      strDate.value = (busQj.stockYear+''+busQj.stockMonth)
      endDate.value = (busQj.stockYear+''+busQj.stockMonth)
    } else {
      // 获取最大凭证期间
      const qijian = ''
      if (hasBlank(qijian)) {
        maxPingzhengQj.value = ''
        strDate.value = dateList.value[0].value
        endDate.value = dateList.value[dateList.value.length - 1].value
      } else {
        maxPingzhengQj.value = qijian.substring(0, 4) + '-' + qijian.substring(4, 6)
        strDate.value = maxPingzhengQj.value
        endDate.value = maxPingzhengQj.value
      }
    }
    //  人员
    // await reLifeEtcList(currentDynamicTenant.value)

  } else {
    await pointMessage({title: '温馨提示', content: '请先进行账套年度授权后再进行查询操作！', delay: true})
    currentDynamicTenant.value = ''
    strDate.value = ''
    endDate.value = ''
  }
}


async function reLifeEtcList(dynamicTenant) {
  let q = ['user', 'cust', 'dept', 'operator', 'warehouse', 'stock', 'whClass']
  dataMap.value = (await useRouteApi(findStockCaiGouList, {schemaName: currentDynamicTenant})([...new Set(q)].join(',')))
}

const oldStrYear = ref('')

async function handleChangeStrDate() {
  endDate.value = strDate.value

  return false;
  if (strDate.value === undefined) {
    endDate.value = '';
    timeselflg.value = false;
  }
  // 开始日期变动  如果开始日期大于结束于日期 则重置结束日期
  if (endDate.value) {
    if (strDate.value > endDate.value) {
      endDate.value = '';
    }
    timeselflg.value = true;
  }
}

function timechange() {
  dateselflg.value = time.value.length > 0;
}

async function handleChangeEndDate() {
  if (endDate.value === undefined) {
    strDate.value = '';
    timeselflg.value = false;
  }
  // 开始日期变动  如果结束日期小于开始日期 则重置开始日期
  if (strDate.value) {
    if (strDate.value > endDate.value) {
      strDate.value = '';
    }
    /*  // 会计科目
      await reLifeKmList(currentDynamicTenant.value)
      timeselflg.value = true;*/
  }
}

async function focusStrDate() {
  /*if (endDate.value) {
    strDateList.value = dateList.value.filter(o => o.value <= endDate.value)
  } else {
    strDateList.value = dateList.value
  }*/
  strDateList.value = dateList.value
}

async function focusEndDate() {
  if (strDate.value) {
    endDateList.value = dateList.value.filter(o => o.stockYear+''+o.stockMonth >= strDate.value)
  } else {
    endDateList.value = dateList.value
  }
}

const confirm = () => {

}
const cancel = () => {

}
/*********Mr·Ye*********/
const modelLoadIng = ref(false)

const defaultModel = {
  constant: {
    tenantId: '',
    name: '',
    queryType: '1',
  },
  variable: {
    xsShXkd: '',
    periodStart: '',
    periodEnd: '',
    dateStart: '',
    dateEnd: '',
    ccodeMin: '',
    ccodeMax: '',
    cvencode: '',
    bcheck: '',
    cinvode: '',
    cinvodeClass: '',
    batchId: '',
    dvdate: '',
    cwhcode: '',
    cpersoncode: '',
    cdepcode: '',
    citemcode: '',
  }
}

const queryModel = reactive({constant: {}, variable: {}})

const openOrClose = (visible: boolean) => {
  // 初始化选中
}
/*********Mr·Ye*********/
let isChanged: boolean = false

async function handleOk() {
   if (dateList.value.length === 0) {
    message.error('请先初始化会计期间后再进行查询!');
    return
  }
  let startQj = strDate.value || ''
  let endQj = endDate.value || ''
  let startRq = null == riqi.value[0] ? '' : riqi.value[0].format('YYYY-MM-DD')
  let endRq = null == riqi.value[1] ? '' : riqi.value[1].format('YYYY-MM-DD')
  if ((startQj == '' && endQj == '') && riqi.value.length === 0) {
    message.error('请选择会计区间or日期期间!');
    return
  }
  if ((startQj != '' && endQj == '') || (startQj == '' && endQj != '')) {
    message.error('请完善会计区间');
    return
  } else if (ifCrossYear.value != '2' && (startQj != '' && endQj != '' && (startQj.substring(0, 4) != endQj.substring(0, 4)))) {
    message.error('暂不支持跨越年度会计区间查询');
    return
  } else if (ifCrossYear.value != '2' && (startRq != '' && endRq != '' && (startRq.substring(0, 4) != endRq.substring(0, 4)))) {
    message.error('暂不支持跨越年度日期区间查询');
    return
  }
  let pingS = queryModel.variable.ccodeMin
  let pingE = queryModel.variable.ccodeMax
  if (pingS != '' || pingS != '') {
    if (pingS == '' || pingE == '') {
      message.error('请完善凭证编号区间条件!');
      return
    } else if (Number(pingE) < Number(pingS)) {
      message.error('编号区间条件必须自小到大!');
      return
    }
  }

  let nMin = queryModel.variable.numberMin
  let nMax = queryModel.variable.numberMax
  if ((nMin != '' && nMax == '') || nMax != '' && nMin == '') {
    message.error('请完善数量区间条件!');
    return
  } else if (parseFloat(nMin) > parseFloat(nMax)) {
    message.error('数量条件必须自小到大!');
    return
  }
  queryModel.variable.periodStart = startQj
  queryModel.variable.periodEnd = endQj
  queryModel.variable.dateStart = startRq
  queryModel.variable.dateEnd = endRq
  //   modifyDbPersonData(queryModel);  记录个人习惯
  modelLoadIng.value = false
  emit('query', {data: unref(queryModel),map: dataMap.value,dynamic:dynamicTenant.value})
  closeModal()
  return true
}

async function handleClose() {
  if (null != formItems.value.openOne && formItems.value.openOne == 1)
    await closeCurrent()
  closeModal()
}

// 查询

let radiovalue = ref('1');
const riqi: any = ref([]);

function radiochange(val) {
  timeselflg.value = val.target.value === '1';
  dateselflg.value = val.target.value === '2';
  if (val.target.value === '1') {
    riqi.value = '';
    strDate.value = maxPingzhengQj.value;
    endDate.value = maxPingzhengQj.value;
  } else {
    strDate.value = '';
    endDate.value = '';
  }
}

// 过滤设置
const transferHandleChange = (nextTargetKeys: string[]) => {
  targetKeys.value = nextTargetKeys;
}

const transferHandleSelectChange = (sourceSelectedKeys: string[], targetSelectedKeys: string[]) => {
  selectedKeys.value = [...sourceSelectedKeys, ...targetSelectedKeys];
}

const dbQueryJson = reactive({ // 后台数据模型
  id: null,
  accountId: '',
  owningMenuName: '销货单列表',
  owningPlan: '1',
  planPerson: userId,
  filterConditions: '',
  queryConditions: ''
})

/**
 * 针对与凭证查询条件清理无用参数
 */
const modifyDbPersonData = async (jsonData) => { // 异步更新后台最新记录
  let json = JSON.parse(JSON.stringify(jsonData))
  delete json['authority']
  delete json['constant']['thisAdInfo']
  json.variable = ObjTool.dels(json.variable, [])
  dbQueryJson.filterConditions = JSON.stringify({'filter1': (targetKeys.value)});
  dbQueryJson.queryConditions = JSON.stringify(json);
  modifyParameterValue(dbQueryJson).then(() => {
    message.success('查询参数后台同步成功！')
  }).catch(() => {
    message.warn('查询参数后台同步失败！')
  });
}

/**
 * 针对与凭证查询条件是否改变
 */
const modelNoChange = () => {
  let constant = queryModel.constant
  if (!hasBlank(dbQueryJson.queryConditions)) {
    let map = JSON.parse(dbQueryJson.queryConditions)
    let variableJSON = JSON.stringify(ObjTool.dels(queryModel.variable, []))
    let s = StrTool
    let flag = s.eq(constant.companyCode, map.constant.companyCode) && s.eq(constant.currency, map.constant.currency) && s.eq(constant.tenantId, map.constant.tenantId)
      && s.eq(variableJSON, JSON.stringify(map.variable))
    return flag;
  }
  return false;
}
/*辅助核算过滤框*/

const checkChange = (e) => {
  let v = e.target.value
  let dates = [strDate.value.substring(0,4),strDate.value.substring(4,6)]
  switch (v) {
    case '1':
      let the = parseInt(dates[1])
      if (the != 1) {
        strDate.value = dates[0]  + (((the - 1) > 9) ? `${(the - 1)}` : `0${(the - 1)}`)
        endDate.value = strDate.value
      } else {
        message.info('暂无上月！')
      }
      break
    case '2':
      strDate.value = dates[0]  + busDate.split('-')[1]
      endDate.value = strDate.value
      break
    case '3':
      let qList = quarterList.value.filter(it => it.iyear == dates[0])
      if (qList.length > 0) { // 存在季报
        let arr = qList.filter(it => {
          let min = moment(it.iyear + '-' + it.dateStart, 'YYYY-MM-DD')
          let max = moment(it.iyear + '-' + it.dateEnd, 'YYYY-MM-DD')
          let the = moment(strDate.value + '-01', 'YYYY-MM-DD')
          return min <= the && the <= max
        })
        strDate.value = arr[0].iyear + '-' + arr[0].dateStart.substring(0, 2)
        endDate.value = arr[0].iyear + '-' + arr[0].dateEnd.substring(0, 2)
      } else {
        let min = '01'
        let max = '03'
        let v = dates[1]
        switch (v) {
          case '04':case '05':case '06':
            min = '04'
            max = '06'
            break
          case '07' :case '08' :case '09':
            min = '07'
            max = '09'
            break
          case '10' :case '11' :case '12':
            min = '10'
            max = '12'
            break
        }
        strDate.value = dates[0] +  min
        endDate.value = dates[0] +  max
      }
      break
    case '4':
      let list = dateList.value.filter(it => it.stockYear == dates[0])
      strDate.value = dates[0]+list[0].stockMonth
      endDate.value = dates[0]+list[list.length - 1].stockMonth
      break
    case '5':
      let d = dayjs(busDate, 'YYYY-MM-DD')
      riqi.value = [d, d]
      break
    case '6':
      let yday = dayjs(busDate, 'YYYY-MM-DD').add(-1, 'day')
      riqi.value = [yday, yday]
      break
    case '7':
      let yday1 = dayjs(busDate, 'YYYY-MM-DD').add(-2, 'day')
      riqi.value = [yday1, yday1]
      break
    case '8':
      let yday2 = dayjs(busDate, 'YYYY-MM-DD').endOf('month')
      riqi.value = [yday2, yday2]
      break
  }
}
const thisEditType = ref('')
const [registerModalPopPage, {openModal: openMoalPopPage}] = useModal();
const [registerSelectDeptPage, {openModal: openSelectDeptPage}] = useModal()
const [registerStockCangKuModalPage, {openModal: openStockCangKuModalPage}] = useModal();
const [registerSelectPsnPage, {openModal: openSelectPsnPage}] = useModal()
const [registerStockInfoModalPage, {openModal: openStockInfoModalPage}] = useModal();
const [registerClassAllPage, { openModal: openClassAllPage }] = useModal();
const openHeadSelectContent = (type) => {
  thisEditType.value = type
  switch (type) {
    case 'cvencode':
      openMoalPopPage(true, {
        database: currentDynamicTenant.value,
        accId: dbQueryJson.accountId,
      });
      break;
    case 'cdepcode':
      openSelectDeptPage(true, {
        currentDynamicTenant: currentDynamicTenant.value
      })
      break;
    case 'cwhcode':
      openStockCangKuModalPage(true, {
        database: currentDynamicTenant.value,
      })
      break;
    case 'cpersoncode':
      openSelectPsnPage(true, {
        currentDynamicTenant: currentDynamicTenant.value
      })
      break;
    case 'cinvode':
      openStockInfoModalPage(true, {
        database:currentDynamicTenant.value
      });
      break;
    case 'cinvodeClass':
        openClassAllPage(true, {
          database:currentDynamicTenant.value
        });
        break;
  }
}
const modalData = (o) => {
  queryModel.variable[thisEditType.value] = thisEditType.value == 'cvencode' || thisEditType.value == 'cwhcode' ? o[0].id : thisEditType.value == 'cdepcode' ? o.uniqueCode : o.id
}
</script>
<style lang="less" scoped>
@import '/@/assets/styles/redTitle-open.less';
.nc-open-content {
  background-image: url(/@/assets/images/homes/bg-pattern.png);
  background-repeat: no-repeat;
  background-position: 66% 8%;
  background-size: contain;
  position: relative;
  overflow-x: hidden;


  .open-content-up {
    position: relative;
    display: inline-flex;justify-content: space-between;
    .right-btns{
      width: 150px;background-color: #f1f1f1;padding: 10% 4%;height: 475px;
      :deep(.ant-btn-primary:hover){
        border: 1px solid #5f375c;
      }
    }
    ul {
      padding: 0 10px;

      li {
        margin: 10px 0;

        span {
          font-size: 14px;
          color: #747272;
        }

        .right_span{
          display: inline-block;
          width: 90px !important;
        }

        /*> span:nth-of-type(1), .right_span {
          display: inline-block;
          width: 120px;
        }*/

        .ant-select {
          font-size: 14px;
        }
      }

    }
  }

  .ant-select-selection-search-input {
    border-bottom: none !important;
  }

  .ant-input:focus {
    box-shadow: none;
  }

  :deep(.ant-input) {
    background: none !important;
  }

  :deep(.ant-select-selector), :deep(.ant-input-affix-wrapper) {
    border: none !important;
    border-bottom: 1px solid #bdb9b9 !important;
    background: none;
  }

  label:not(.ant-radio-button-wrapper) {
    text-align: right;
    width: 110px;
    display: inline-block;
    padding: 5px 10px;
  }

  .ant-radio-group {
    .ant-radio-wrapper {
      width: 70px;

      .ant-radio-input {
        border-color: slategrey;
      }
    }

    p:nth-of-type(2) {
      margin-bottom: 0;
    }
  }

  :deep(.ant-picker-range) {
    background: none;
    border: none;
    border-bottom: 1px solid #bdb9b9;

    input {
      text-align: center;
    }
  }

  .special-border-div {
    position: relative;
    border: 1px #acabab solid;
    margin: 2.5% 1%;

    > span {
      position: absolute;
      top: -12px;
      left: 50px;
      font-size: 15px;
      background: white;
      padding: 0 15px;
    }

    > div {
      margin: 15px;
      color: black;
      display: flex;

      .sbd-left {
        width: 60%;

        .ant-radio-group {
          .ant-radio-wrapper {
            width: 11% !important;
            text-align: left;

            .ant-radio-input {
              border-color: slategrey;
            }
          }
        }
      }

      .sbd-right {
        width: 40%;
        padding: 2% 5% 0;

        > div:nth-of-type(2) {
          margin-top: 14px;
        }

        .ant-radio-button-wrapper {
          color: #0096c7;
        }
      }

    }
  }
}
</style>
