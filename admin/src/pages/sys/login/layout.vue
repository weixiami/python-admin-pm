<template>
    <d2-container>
        <el-form
            :inline="true"
            slot="header"
            size="mini"
        >
            <el-form-item>
                <el-select
                    v-model="value"
                    placeholder="请选择"
                    clearable
                    size="mini"
                    :clear="clearStatus"
                >
                    <el-option
                        v-for="item in statusOption"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value"
                    >
                    </el-option>
                </el-select>
            </el-form-item>
            <el-form-item>
                <el-button
                    icon="el-icon-search"
                    size="mini"
                    type="primary"
                    @click="changeStatus"
                ></el-button>
            </el-form-item>
            <el-form-item>
                <el-button
                    icon="el-icon-refresh-right"
                    size="mini"
                    @click="init"
                    circle
                    title="刷新"
                ></el-button>
            </el-form-item>
        </el-form>

        <el-table
            :data="logData"
            style="width: 100%"
            size="mini"
            type="ghost"
            v-loading="loading"
        >
            <el-table-column
                prop="username"
                label="用户名"
                align="center"
            >
            </el-table-column>
            <el-table-column
                prop="type"
                label="操作类型"
                align="center"
            >
                <template slot-scope="scope">
                    <el-tag size="mini">登录</el-tag>
                </template>
            </el-table-column>
            <el-table-column
                prop="status"
                label="状态"
                align="center"
            >
                <template slot-scope="scope">
                    <el-tag
                        size="mini"
                        type="success"
                        v-if="scope.row.status == 0"
                    >成功</el-tag>
                    <el-tag
                        size="mini"
                        type="danger"
                        v-else-if="scope.row.status == 1"
                    >失败</el-tag>
                    <el-tag
                        size="mini"
                        type="info"
                        v-else
                    >隐藏</el-tag>
                </template>
            </el-table-column>
            <el-table-column
                prop="ip"
                label="操作Ip"
                align="center"
            >
            </el-table-column>
            <el-table-column
                prop="create_time"
                label="创建时间"
                align="center"
            >
            </el-table-column>
        </el-table>

        <Pagination
            slot="footer"
            :page="page"
            :total="total"
            :size="size"
            @handleSize="handleSize"
            @handleCurrent="handleCurrent"
        ></Pagination>
    </d2-container>
</template>

<script>
import { QueryLogByParam } from '@api/sys.log'
import Pagination from '@/pages/pagination/index.vue'
export default {
    name: 'sys-login-log',
    components: {
        Pagination
    },
    data() {
        return {
            logData: [],
            page: 1,
            total: 0,
            size: 20,
            value: '',
            status: '',
            statusOption: [
                { label: '成功', value: 0 },
                { label: '失败', value: 1 },
                { label: '隐藏', value: 2 }
            ],
            loading: false
        }
    },
    created() {
        this.init()
    },
    methods: {
        init() {
            let params = {
                type: [1], // 登录类型
                page: this.page,
                page_size: this.size
            }
            if (this.status != '') params['status'] = [this.status]

            this.loading = true
            QueryLogByParam(params)
                .then(async res => {
                    this.loading = false
                    this.logData = res.data
                    this.total = res.total
                })
                .catch(() => {
                    this.loading = false
                })
        },
        changeStatus() {
            this.status = this.value
            this.init()
        },
        clearStatus() {
            this.value = ''
        },
        handleSize(size) {
            this.size = size
            this.page = 1
            this.init()
        },
        handleCurrent(page) {
            this.page = page
            this.init()
        }
    }
}
</script>

<style scoped>
.el-select {
    width: 120px;
}

.el-form-item--mini.el-form-item {
    margin-bottom: 0;
}
</style>