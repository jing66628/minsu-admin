<template>
  <div class="app-container">
    <el-table v-loading="listLoading" :data="list" element-loading-text="Loading" border fit highlight-current-row>
      <el-table-column type="index" width="50" label="编号" align="center" />
      <el-table-column align="center" label="订单号">
        <template slot-scope="scope">
          {{ scope.row.orderNo }}
        </template>
      </el-table-column>

      <el-table-column label="房间信息" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.orderBody }}</span>
        </template>
      </el-table-column>

      <el-table-column align="center" label="房间图片" width="80">
        <template slot-scope="scope">
          <el-image style="width: 50px; height: 50px" :src="baseURL + scope.row.orderImage" />
        </template>
      </el-table-column>

      <el-table-column label="预定人" align="center" width="80">
        <template slot-scope="scope">
          <span>{{ scope.row.reserveName }}</span>
        </template>
      </el-table-column>

      <el-table-column label="预定人手机号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.reservePhone }}</span>
        </template>
      </el-table-column>


      <el-table-column label="预定时间" align="center" width="110">
        <template slot-scope="scope">
          <span>{{ scope.row.orderReserveTimeStart | dateTime }} - {{ scope.row.orderReserveTimeEnd | dateTime }}</span>
        </template>
      </el-table-column>
      <el-table-column label="预计到店时间" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.estimateArriveTime }}</span>
        </template>
      </el-table-column>

      <el-table-column label="备注" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.orderRemark }}</span>
        </template>
      </el-table-column>

      <el-table-column label="订单金额" align="center" width="80">
        <template slot-scope="scope">
          <span>{{ scope.row.orderPrice }}</span>
        </template>
      </el-table-column>

      <el-table-column label="实付金额" align="center" width="80">
        <template slot-scope="scope">
          <span>{{ scope.row.orderPricePay }}</span>
        </template>
      </el-table-column>

      <el-table-column label="订单状态" align="center">
        <template slot-scope="scope">
          <span v-if="scope.row.orderState == 0">未入住</span>
          <span v-if="scope.row.orderState == 1">已入住</span>
        </template>
      </el-table-column>

      <el-table-column label="支付时间" align="center" width="110">
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span>{{ scope.row.orderPayTime }}</span>
        </template>
      </el-table-column>

      <el-table-column label="创建时间" align="center" width="110">
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span>{{ scope.row.orderCreateTime }}</span>
        </template>
      </el-table-column>

      <el-table-column label="操作" align="center" width="200" class-name="small-padding fixed-width">
        <template slot-scope="{row}">
          <el-button size="small" type="danger" @click="handleWriteOff(row)">
            核销
          </el-button>
        </template>
      </el-table-column>

    </el-table>
    <pagination v-show="total>0" :total="total" :page.sync="listQuery.page" :limit.sync="listQuery.pageSize"
      @pagination="fetchData" />

  </div>
</template>

<script>
  import {
    orderTableList,
    roomReserveRecordWriteOff
  } from '@/api/order'
  import {
    roomList
  } from '@/api/room'
  import Pagination from '@/components/Pagination'

  export default {
    components: {
      Pagination
    },
    data() {
      return {
        list: null,
        listLoading: false,
        total: 0,
        listQuery: {
          page: 1,
          pageSize: 10
        }
      }
    },
    created() {
      this.fetchData()
    },
    filters: {
      //处理函数
      dateTime(value) {
        return value.split(' ')[0]
      }
    },
    methods: {
      fetchData() { // 查
        this.listLoading = true
        orderTableList(this.listQuery).then(res => {
          this.list = res.data.records
          this.total = res.data.total
          this.listLoading = false
        })
      },
      handleWriteOff(row) {
        roomReserveRecordWriteOff(row).then(() => {
          this.fetchData()
          this.$notify({
            title: '成功',
            message: '核销成功',
            type: 'success',
            duration: 2000
          })
        })
      },
      // handleFilter() {
      //   this.listQuery.page = 1
      //   this.fetchData()
      // },
      // handleReset() {
      //   this.listQuery = {
      //     page: 1,
      //     pageSize: 10
      //   }
      //   this.fetchData()
      // }
    }
  }
</script>
