<template>
  <a-modal :title="(add ? $t('Add ') : $t('Edit ')) + interface_name" :visible="true" @cancel="handleCancel" :footer="null">
    <vuci-form uci-config="vuci_components_task">
      <vuci-named-section :name="name" v-slot="{ s }">
        <vuci-form-item-select :uci-section="s" name="proto" :label="$t('Protocol')" :options="protocols"/>
        <vuci-form-item-input :uci-section="s" name="address" :label="$t('Address')" rules="ip4addr" required/>
        <vuci-form-item-input :uci-section="s" name="netmask" :label="$t('Netmask')" rules="netmask4" required/>
        <vuci-form-item-input :uci-section="s" name="gateway" :label="$t('Gateway')" rules="ip4addr"/>
        <vuci-form-item-list :uci-section="s" name="dns" :label="$t('DNS')" rules="ip4addr"/>
      </vuci-named-section>
    </vuci-form>
  </a-modal>
</template>

<script>
export default {
  name: 'modal',
  data () {
    return {
      protocols: [
        ['static', this.$t('Static')],
        ['dhcp', this.$t('DHCP')]
      ]
    }
  },
  props: {
    add: {
      type: Boolean,
      default: false
    },
    name: {
      type: String,
      default: ''
    },
    interface_name: {
      type: String,
      default: ''
    }
  },
  methods: {
    handleCancel () {
      this.$emit('cancelEmitted')
    }
  }
}
</script>
