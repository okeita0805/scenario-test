<template>
  <div id="app">
    <el-container>
      <el-main>
        <el-row :gutter="20">
          <el-col :span="24">
            <transition-group name="list" tag="div">
              <drag
                class="drag"
                v-for="form in forms"
                :key="form.value"
                :data="form">
                <el-button circle :type="form.color" :icon="form.icon" />
              </drag>
            </transition-group>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="24">
            <drop @drop="drop">
              <el-card >
                <vue-blocks-container
                  ref="container"
                  class="form-container"
                  :blocks-content.sync="blocks"
                  @blockSelect="selectBlock"
                  @blockDeselect="deselectBlock"
                  :scene.sync="scene" />
                 <vue-block-property :property="selectedBlockProperty" />
              </el-card>
            </drop>
          </el-col>
        </el-row>
      </el-main>
    </el-container>
    <!-- ドロップ後に表示されるモーダル -->
    <el-dialog
      :title="dialog.title"
      :visible.sync="dialog.isShow"
      width="30%">
      <el-form>
        <el-form-item>
          <el-input
            rows="10"
            type="textarea"
            v-model="dialog.body" />
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialog.isShow = false">Cancel</el-button>
        <el-button type="primary" @click="dialog.isShow = false">Confirm</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { Drag, Drop, DropMask } from 'vue-easy-dnd';

export default {
  name: 'App',
  components: {
    Drag,
    Drop,
    DropMask,
  },
  data() {
    return {
      blocks: [
        {
          name: 'test1',
          title: 'Test block',
          family: 'Test',
          description: 'Description text',
          fields: [
            {
              name: 'in1',
              type: 'event',
              attr: 'input',
            },
            {
              name: 'in2',
              type: 'event',
              attr: 'input',
            },
            {
              name: 'out1',
              type: 'event',
              attr: 'output',
            },
            {
              name: 'out2',
              type: 'event',
              attr: 'output',
            },
          ],
        },
        {
          name: 'test2',
          title: 'Test block',
          family: 'Test',
          description: 'Description text',
          fields: [
            {
              name: 'in1',
              type: 'event',
              attr: 'input',
            },
          ],
        },
      ],
      scene: {
        blocks: [
          {
            id: 1,
            x: -200,
            y: 0,
            name: 'test1',
            title: '質問1',
            values: {
              property: [
                {
                  name: 'message',
                  type: 'string',
                },
              ],
            },
          },
          {
            id: 2,
            x: 200,
            y: 0,
            name: 'test2',
            title: '質問2',
            values: {
              property: [
                {
                  name: 'message',
                  type: 'string',
                },
              ],
            },
          },
        ],
        links: [
          {
            id: 1,
            originID: 1,
            originSlot: 0,
            targetID: 2,
            targetSlot: 0,
          },
        ],
        container: {
          centerX: 0,
          centerY: 0,
          scale: 1,
        },
      },
      selectedBlock: null,
      dialog: {
        isShow: false,
        title: 'フォームの追加',
        body: '',
      },
      forms: {
        text: {
          icon: 'el-icon-edit',
          value: 'text',
          color: 'primary',
        },
        radio: {
          icon: 'el-icon-dish',
          value: 'radio',
          color: 'success',
        },
        select: {
          icon: 'el-icon-milk-tea',
          value: 'select',
          color: 'warning',
        },
      },
      copied: [],
    };
  },
  computed: {
    selectedBlockProperty() {
      if (!this.selectedBlock || !this.selectedBlock.values
      || !this.selectedBlock.values.property) {
        return null;
      }
      return this.selectedBlock.values.property;
    },
    selectBlocksType() {
      return this.blocks.map(b => b.family)
        .filter((value, index, array) => array.indexOf(value) === index);
    },
  },
  methods: {
    selectBlock(block) {
      this.selectedBlock = block;
    },
    deselectBlock() {
      this.selectedBlock = null;
    },
    drop(e) {
      this.dialog.isShow = true;
      console.log(e.data);
      this.$refs.container.addNewBlock('test1');
    },
  },
};
</script>

<style>

.drag {
  width: 60px;
  height: 60px;
  display:inline-block;
}

.form-container {
  height: 70vh;
}
</style>
