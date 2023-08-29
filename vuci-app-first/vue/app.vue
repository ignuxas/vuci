
<template>
    <div>
        <div :key="counter">
            <modal v-if="modalVisible" :add="add" :name="name" :interface_name="interface_name" @cancelEmitted="handleCancel"></modal>
            <vuci-form uci-config="vuci_components_task">
                <vuci-typed-section type="interface" :columns="columns">
                    <template #interface_name="{ s }"> <!-- Interface name -->
                        <div style="text-align: center;"><b>{{ s.interface_name }}</b></div>
                    </template>
                    <template #address="{ s }"> <!-- Address -->
                        <vuci-form-item-input :uci-section="s" name="address" rules="ip4addr"/>
                    </template>
                    <template #netmask="{ s }"> <!-- Netmask -->
                        <vuci-form-item-input :uci-section="s" name="netmask" rules="netmask4"/>
                    </template>
                    <template #actions="{ s }"> <!-- Actions -->
                        <a-button type="primary" size="small" style="margin-right: 5px" @click="showEdit(s)">{{ $t('Edit') }}</a-button>
                        <a-popconfirm @confirm="del(s['.name'])" placement="left" :title="$t('interfaces.DelConfirm')">
                            <a-button type="danger" size="small">{{ $t('Delete') }}</a-button>
                        </a-popconfirm>
                    </template>
                </vuci-typed-section>
                <div>
                    <a-input v-model="add.interface_name" :placeholder="$t('Interface Name')" :style="{width: '200px', 'margin-right':'20px'}"/>
                    <a-button type="primary" style="margin-top: 10px" @click="uciAdd">+ {{ $t('interfaces.Add interface') }}</a-button>
                </div>
            </vuci-form>
        </div>
    </div>
</template>

<script>
import modal from './interfaces/modal.vue'

export default {
    name: 'vuci-app-components',
    components: {
        modal,
    },

    data() {
        return {
            counter: 0,
            labelCol: { span: 8 },
            wrapperCol: { span: 14 },
            add: false,
            modalVisible: false,
            name: '',
            interface_name: '',
            add: {
                interface_name: '',
            },
            columns: [
                { name: 'interface_name', label: 'Interface Name'},
                { name: 'address', label: 'Address'},
                { name: 'netmask', label: 'Netmask'},
                { name: 'actions', label: 'Actions'}
            ],
        }
    },

    methods: {
        uciAdd() {
            this.$spin()
            const name = this.$uci.add('vuci_components_task', 'interface')
            const interface_name = this.add.interface_name
            this.$uci.set('vuci_components_task', name, 'interface_name', interface_name)
            this.$uci.save().then(() => {
                this.$uci.apply().then(() => {
                    this.$uci.load('vuci_components_task').then(() => { // reload to get the new section
                        const interfaces = this.$uci.sections('vuci_components_task', 'interface')
                        this.name = interfaces[interfaces.length - 1]['.name']
                        this.interface_name = interfaces[interfaces.length - 1].interface_name
                        this.showAdd()
                        this.reLoad()
                        this.$spin(false)
                    })
                })
            })
        },
        showAdd() {
            this.add = true
            this.modalVisible = true
        },
        showEdit(s) {
            this.add = false
            this.modalVisible = true
            this.name = s['.name']
            this.interface_name = s.interface_name
        },
        handleCancel() {
            this.modalVisible = false
            this.reLoad()
        },
        reLoad() {
            this.counter += 1
        },
        del(name) {
            this.$spin()
            this.$uci.del('vuci_components_task', name)
            this.$uci.save().then(() => {
                this.$uci.apply().then(() => {
                    this.$spin(false)
                    this.reLoad()
                })
            })
        }
    }
}

</script>
