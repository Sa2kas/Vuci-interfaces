<template>
  <div class="typed-section-page">
    <vuci-form uci-config="interfaces" :key="rerender">
      <vuci-typed-section type="interface" :columns="interfaces.columns">
        <template #name="{ s }">
          <vuci-form-item-dummy :uci-section="s" style="margin-bottom: -10px" name="name"/>
        </template>
        <template #address="{ s }">
          <vuci-form-item-dummy :uci-section="s" style="margin-bottom: -10px" name="address"/>
        </template>
        <template #netmask="{ s }">
          <vuci-form-item-dummy :uci-section="s" style="margin-bottom: -10px" name="netmask" />
        </template>
        <template #edit="{ s }">
          <a-button type="primary" size="small" style="margin-right: 5px" @click="editItem(s)">{{ $t('Edit') }}</a-button>
          <a-popconfirm @confirm="del(s['.name'])" placement="left" :title="$t('interfaces.DelConfirm')">
            <a-button type="danger" size="small">{{ $t('Delete') }}</a-button>
          </a-popconfirm>
        </template>
      </vuci-typed-section>
      <template #footer>
        <div class="table-footer">
          <div class="footer-title">
            Add interface
          </div>
          <div style="display: flex; flex-direction: horizontal; justify-content: space-between; text-transform: uppercase;">
            <span style="margin-top: 5px">
              Interface name
            </span>
            <input type="text" v-model="name" class="create">
            <a-button :disabled="!name" @click="handleAdd">Create</a-button>
          </div>
        </div>
      </template>
    </vuci-form>
    <vuci-modal-form :isModalVisible="isModalVisible" :item="sec" @close="closeModal()" :modalTitle="modalTitle"></vuci-modal-form>
  </div>
</template>

<script>
import VuciModalForm from './interfaces/vuci-modal-form.vue'
export default {
  components: {
    VuciModalForm
  },
  data () {
    return {
      time: '',
      configFile: 'interfaces',
      typed: {},
      interfaces: {
        columns: [
          { name: 'name', label: 'Interface name' },
          { name: 'address', label: 'Address' },
          { name: 'netmask', label: 'Netmask' },
          { name: 'edit', label: '', width: 150 }
        ],
        data: []
      },
      protos: [
        'static',
        'dhcp'
      ],
      modalTitle: '',
      sec: {},
      isModalVisible: false,
      name: '',
      sections: [],
      rerender: true,
      iFace: {}
    }
  },
  methods: {
    async load () {
      await this.$uci.load(this.configFile)
    },
    async getInterface () {
      const iFaces = this.$uci.sections(this.configFile, 'interface')
      const iFace = iFaces[iFaces.length - 1]
      this.editItem(iFace)
    },
    validateName () {
      if (this.name.length > 25) {
        return 'Name is too long'
      }
      const interfaces = this.$uci.sections(this.configFile, 'interface')
      for (let index = 0; index < interfaces.length; index++) {
        if (interfaces[index].name === this.name) {
          return 'Name already used'
        }
      }
    },
    handleAdd () {
      if (this.validateName) {
        this.$message.error(this.validateName)
        return
      }
      const id = this.$uci.add(this.configFile, 'interface')
      this.$uci.set('interfaces', id, 'name', this.name)
      this.$uci.set('interfaces', id, 'proto', 'static')
      this.$uci.set('interfaces', id, 'dhcp', '1')
      this.$uci.save().then(() => {
        this.$uci.apply().then(() => {
          this.load().then(() => {
            this.getInterface()
          })
          this.name = ''
          this.ReRender()
        })
      })
    },
    del (sid) {
      this.$uci.del(this.configFile, sid)
      this.$uci.save().then(() => {
        this.$uci.apply().then(() => {
          this.ReRender()
        })
      })
    },
    ReRender () {
      this.rerender = !this.rerender
    },
    closeModal () {
      this.isModalVisible = false
      this.ReRender()
    },
    editItem (s) {
      this.sec = s
      this.isModalVisible = true
      this.modalTitle = s.name
    }
  }
}
</script>
<style>
  .footer-title {
    text-transform: uppercase;
    padding: 5px 0;
    font-size: 16px;
  }
  .create {
    outline: none;
    border-radius: 5px;
    height: 30px;
    border: 1px solid #888;
  }
  .create:focus {
    box-shadow: 0 0 2px 1px rgba(24, 144, 255, 0.5);
    border: 1px solid #1890ff;
    outline: none;
  }
</style>
