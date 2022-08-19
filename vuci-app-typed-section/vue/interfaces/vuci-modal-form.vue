<template>
  <div class="vuci-modal-form">
    <vuci-modal
      v-if="isModalVisible"
      @close="close()"
      :name="modalTitle">
        <vuci-form uci-config="interfaces" @applied="check(item['.name'])" :key="rerender">
        <vuci-named-section :name="item['.name']" :card="false" v-slot="{ s }">
          <vuci-form-item-select :uci-section="s" label="Protocol" name="proto" :options="protos" required/>
          <vuci-form-item-input :uci-section="s" label="Address" name="address" rules="ip4addr" depend="proto == 'static'" required/>
          <vuci-form-item-input :uci-section="s" label="Netmask" name="netmask" rules="netmask4" depend="proto == 'static'" required/>
          <vuci-form-item-input :uci-section="s" label="Gateway" name="gateway" rules="ip4addr" depend="proto == 'static'"/>
          <vuci-form-item-list :uci-section="s" label="DNS" name="dns" rules="ip4addr" depend="proto == 'static'"/>
          <vuci-form-item-switch :uci-section="s" label="DHCP" name="dhcp" :initial="true" depend="proto == 'static'"/>
        </vuci-named-section>
        <div class="line" style="margin-bottom: 10px; margin-left: -40px; width: calc(100% + 78px)"/>
      </vuci-form>
    </vuci-modal>
  </div>
</template>
<script>
import VuciModal from './VuciModal.vue'
export default {
  components: { VuciModal },
  data () {
    return {
      protos: [
        'static',
        'dhcp'
      ],
      rerender: true
    }
  },
  props: {
    modalTitle: {
      type: String,
      default: '',
      required: false
    },
    isModalVisible: {
      type: Boolean,
      default: false
    },
    item: {
      type: Object,
      required: false
    }
  },
  methods: {
    check (id) {
      const proto = this.$uci.get('interfaces', id, 'proto')
      if (proto === 'dhcp') {
        this.$uci.set('interfaces', id, 'address')
        this.$uci.set('interfaces', id, 'netmask')
        this.$uci.set('interfaces', id, 'gateway')
        this.$uci.set('interfaces', id, 'dns')
        this.$uci.set('interfaces', id, 'dhcp')
        this.save()
        if (proto === 'static' && this.$uci.get('interfaces', id, 'dhcp') === undefined) {
          this.$uci.set('interfaces', id, 'dhcp', '1')
          this.save()
        }
      }
    },
    save () {
      this.$uci.save().then(() => {
        this.$uci.apply().then(() => {
          this.rerender = !this.rerender
        })
      })
    },
    close: function () {
      this.$emit('close')
    }
  }
}
</script>
