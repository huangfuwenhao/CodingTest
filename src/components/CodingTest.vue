<template>
  <div class="container">
    <el-row type="flex" justify="center" :gutter="20">
      <el-col :xs="18" :lg="12"><el-input v-model.trim="blockHash" placeholder="请输入内容"></el-input></el-col>
      <el-col :xs="6" :lg="2"
        ><el-button type="primary" :disabled="!blockHash" @click="search" v-loading.fullscreen.lock="fullscreenLoading"
          >搜索</el-button
        ></el-col
      >
    </el-row>
    <h2>Block {{ blockData.height }}</h2>
    <div class="block">
      <el-row>
        <el-col :xs="8" :lg="4"><p>Hash</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p id="hash" class="text-ellipsis">
            {{ blockData.hash }}<i class="el-icon-s-order copy" data-clipboard-target="#hash"></i>
          </p>
        </el-col>
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Confirmations</p></el-col>
        <el-col :xs="16" :lg="16"><p></p></el-col>
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Timestamp</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.time | dateFormat }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Height</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.height }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Miner</p></el-col>
        <el-col :xs="16" :lg="16"><p></p></el-col>
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Number of Transactions</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.n_tx }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Difficulty</p></el-col>
        <el-col :xs="16" :lg="16"><p></p></el-col>
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Merkle root</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p class="text-ellipsis">{{ blockData.mrkl_root }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Version</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.ver ? '0x' + blockData.ver.toString(16) : '' }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Bits</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.bits | numFormat }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Weight</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.weight | numFormat('WU') }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Size</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.size | numFormat('bytes') }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Nonce</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.nonce | numFormat }}</p></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Transaction Volume</p></el-col>
        <el-col :xs="16" :lg="16"><p></p></el-col>
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Block Reward</p></el-col>
        <el-col :xs="16" :lg="16"><p></p></el-col>
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="4"><p>Fee Reward</p></el-col>
        <el-col :xs="16" :lg="16"
          ><p>{{ blockData.fee | BTCFormat }}</p></el-col
        >
      </el-row>
    </div>
    <h2>Block Transactions</h2>
    <div class="transaction" v-for="(transaction, index) in currentTransactions" :key="index">
      <el-row class="hidden-sm-and-up">
        <el-col :xs="8" :lg="2"><p>Amount</p></el-col>
        <el-col :xs="16" :lg="3"
          ><el-tag effect="dark" type="success">{{ transaction.out | BTCFormatPlus }}</el-tag></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="2"><p>Fee</p></el-col>
        <el-col :xs="16" :lg="19"
          ><p>{{ transaction.fee | BTCFormat }}</p></el-col
        >
        <el-col class="hidden-xs-only" :xs="3" :lg="3"
          ><el-tag effect="dark" type="success">{{ transaction.out | BTCFormatPlus }}</el-tag></el-col
        >
      </el-row>
      <el-row>
        <el-col :xs="8" :lg="2"><p>Hash</p></el-col>
        <el-col :xs="16" :lg="19"
          ><a href="#" class="text-ellipsis">{{ transaction.hash }}</a></el-col
        >
        <el-col class="hidden-xs-only" :xs="3" :lg="3"
          ><p>{{ transaction.time | dateFormat }}</p></el-col
        >
      </el-row>
      <el-row class="hidden-sm-and-up">
        <el-col :xs="8" :lg="2"><p>Date</p></el-col>
        <el-col :xs="16" :lg="3"
          ><p>{{ transaction.time | dateFormat }}</p></el-col
        >
      </el-row>
      <el-row class="hidden-xs-only">
        <el-col :xs="2" :lg="2"><p></p></el-col>
        <el-col :xs="8" :lg="8"
          ><a href="#" class="text-ellipsis" v-for="(item, index) in transaction.inputs" :key="index">
            {{ item.prev_out.addr }}
          </a></el-col
        >
        <el-col :xs="3" :lg="3"
          ><p v-for="(item, index) in transaction.inputs" :key="index">
            {{ item.prev_out.value | BTCFormat }}
            <i class="el-icon-s-help blue"></i></p
        ></el-col>
        <el-col :xs="1" :lg="1"
          ><p><i class="el-icon-right"></i></p
        ></el-col>
        <el-col :xs="7" :lg="7"
          ><a href="#" class="text-ellipsis" v-for="(item, index) in transaction.out" :key="index">
            {{ item.addr }}
          </a></el-col
        >
        <el-col :xs="3" :lg="3"
          ><p v-for="(item, index) in transaction.out" :key="index">
            {{ item.value | BTCFormat }}
            <i class="el-icon-s-help red"></i></p
        ></el-col>
      </el-row>
      <el-row class="hidden-sm-and-up">
        <el-col :xs="8" :lg="2"><p>From</p></el-col>
        <el-col :xs="16" :lg="8"
          ><div class="text-ellipsis" v-for="(item, index) in transaction.inputs" :key="index">
            <a href="#">{{ item.prev_out.addr }}</a>
            <br />
            {{ item.prev_out.value | BTCFormat }}
            <i class="el-icon-s-help blue"></i></div
        ></el-col>
      </el-row>
      <el-row class="hidden-sm-and-up">
        <el-col :xs="8" :lg="2"><p>To</p></el-col>
        <el-col :xs="16" :lg="7"
          ><div class="text-ellipsis" v-for="(item, index) in transaction.out" :key="index">
            <a href="#">{{ item.addr }}</a>
            <br />
            {{ item.value | BTCFormat }}
            <i class="el-icon-s-help red"></i></div
        ></el-col>
      </el-row>
    </div>
    <el-row>
      <el-col :span="20">
        <el-pagination
          background
          layout="prev, pager, next"
          :total="total"
          :pager-count="5"
          @current-change="currentChange">
        </el-pagination>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import moment from 'moment';
import ClipboardJS from 'clipboard';
import 'element-ui/lib/theme-chalk/display.css';
import axios from '../libs/api';

export default {
  data() {
    return {
      // blockHash: '00000000000000000007878ec04bb2b2e12317804810f4c26033585b3f81ffaa',
      blockHash: '',
      tableData: [
        { name: 'Hash', field: 'hash' },
        { name: 'Confirmations', field: '' },
        { name: 'Timestamp', field: 'time' },
        { name: 'Height', field: 'height' },
        { name: 'Miner', field: '' },
        { name: 'Number of Transactions', field: 'n_tx' },
        { name: 'Difficulty', field: '' },
        { name: 'Merkle root', field: 'mrkl_root' },
        { name: 'Version', field: 'ver' },
        { name: 'Bits', field: 'bits' },
        { name: 'Weight', field: 'weight' },
        { name: 'Size', field: 'size' },
        { name: 'Nonce', field: 'nonce' },
        { name: 'Transaction Volume', field: '' },
        { name: 'Block Reward', field: '' },
        { name: 'Fee Reward', field: 'fee' },
      ],
      blockData: {},
      BlockTransactions: [],
      currentTransactions: [],
      total: 0,
      fullscreenLoading: false,
    };
  },
  created() {
    // this.search();
  },
  mounted() {
    new ClipboardJS('.copy');
  },
  filters: {
    dateFormat(val) {
      return moment(val * 1000).format('YYYY-MM-DD HH:mm');
    },
    numFormat(val, unit) {
      if (!val) {
        return val;
      }
      return unit ? val.toLocaleString() + ' ' + unit : val.toLocaleString();
    },
    BTCFormat(val) {
      return (val / 100000000).toFixed(8) + ' BTC';
    },
    BTCFormatPlus(val) {
      var value = 0;
      val.forEach((item) => (value += item.value));
      return (value / 100000000).toFixed(8) + ' BTC';
    },
  },
  methods: {
    async search() {
      this.fullscreenLoading = true;
      const res = await axios.get(`https://blockchain.info/rawblock/${this.blockHash}`);
      console.log(res);
      if (res.status == 200) {
        this.fullscreenLoading = false;
        this.blockData = res.data;
        this.BlockTransactions = res.data.tx;
        this.currentTransactions = this.BlockTransactions.slice(0, 5);
        this.total = Math.ceil(res.data.tx.length / 5);
      } else {
        alert('请重试');
      }
    },
    currentChange(p) {
      this.currentTransactions = this.BlockTransactions.slice((p - 1) * 5, (p - 1) * 5 + 5);
    },
  },
};
</script>

<style>
p {
  margin: 0;
  padding: 10px 0;
}
.container {
  padding: 0 20px;
  margin-bottom: 60px;
}
.block .el-row {
  border-bottom: 1px solid #ccc;
}
.red {
  color: red;
}
.blue {
  color: blue;
}
.text-ellipsis {
  display: inline-block;
  width: 100%;
  white-space: nowrap;
  text-overflow: ellipsis;
  -o-text-overflow: ellipsis;
  overflow: hidden;
}
.transaction {
  margin: 50px 0;
}
</style>
