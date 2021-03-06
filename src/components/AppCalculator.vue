<template>
  <v-container
    grid-list-xl
  >
    <transition
      name="fade"
    >
      <v-layout
        v-if="inputted && inputs.length"
      >
        <v-flex
          v-if="expanded"
        >
          <v-card>
            <v-card-text>
              <div
                v-if="total && tabs.length && groups.length"
              >
                <v-layout>
                  <v-flex>
                    <v-tabs
                      v-if="tabs.length"
                      v-model="tab"
                      grow
                    >
                      <v-tab
                        v-for="(tab, index) in tabs"
                        :key="index"
                        class="title"
                      >
                        {{ tab.total }} 人
                      </v-tab>
                    </v-tabs>
                  </v-flex>
                </v-layout>
                <v-layout
                  row
                  wrap
                >
                  <v-flex
                    v-for="(group, index) in groups"
                    :key="index"
                    sm6
                    xs12
                  >
                    <v-card
                      v-if="group.items.length"
                      height="100%"
                      class="elevation-0"
                    >
                      <v-card-text>
                        <v-layout
                          column
                        >
                          <v-flex>
                            <v-layout
                              row
                              wrap
                            >
                              <v-flex>
                                <div
                                  class="title"
                                >
                                  {{ group.name }}
                                </div>
                              </v-flex>
                              <v-flex
                                class="text-xs-right"
                              >
                                <div
                                  v-if="deviation(group.items, total)"
                                  class="error--text"
                                >
                                  （誤差：{{ deviation(group.items, total) }} 人）
                                </div>
                              </v-flex>
                            </v-layout>
                          </v-flex>
                          <v-divider />
                          <div
                            v-for="(item, index) in group.items"
                            :key="index"
                          >
                            <v-flex
                              v-if="item.value"
                            >
                              <v-layout
                                row
                                wrap
                                align-center
                              >
                                <v-flex
                                  sm2
                                  xs12
                                  class="pa-1 text-sm-right"
                                >
                                  <span
                                    class="default"
                                  >
                                    {{ item.name }}
                                  </span>
                                </v-flex>
                                <v-flex
                                  sm8
                                  xs10
                                  class="py-1"
                                >
                                  <v-progress-linear
                                    color="info"
                                    :value="percentage(item.value, total)"
                                    :height="15"
                                  />
                                </v-flex>
                                <v-flex
                                  sm2
                                  xs2
                                  class="pa-1"
                                >
                                  <v-tooltip
                                    right
                                    color="primary lighten-4 black--text elevation-0"
                                  >
                                    <template
                                      v-slot:activator="{ on }"
                                    >
                                      <span
                                        v-on="on"
                                        class="default"
                                      >
                                        {{ item.value }} 人
                                      </span>
                                    </template>
                                    <span>
                                      {{ percentage(item.value, total) }}%
                                    </span>
                                  </v-tooltip>
                                </v-flex>
                              </v-layout>
                            </v-flex>
                          </div>
                        </v-layout>
                      </v-card-text>
                    </v-card>
                  </v-flex>
                </v-layout>
              </div>
              <div
                v-else
                class="text-xs-center"
              >
                請提供合理的數據。
              </div>
            </v-card-text>
          </v-card>
        </v-flex>
      </v-layout>
    </transition>
    <v-layout
      row
      wrap
    >
      <v-flex>
        <v-card>
          <v-form
            ref="form"
          >
            <v-card-text>
              <v-layout>
                <v-flex>
                  <v-subheader
                    class="px-0"
                  >
                    應徵人數
                  </v-subheader>
                  <v-select
                    v-model="candidate.value"
                    :items="candidates"
                    item-text="label"
                    item-value="value"
                  />
                </v-flex>
              </v-layout>
              <v-layout
                row
                wrap
              >
                <v-flex
                  v-for="(group, index) in fields"
                  :key="index"
                >
                  <v-subheader
                    class="px-0"
                  >
                    {{ group.name }}
                  </v-subheader>
                  <div
                    v-for="(field, index) in group.items"
                    :key="index"
                  >
                    <v-text-field
                      v-model="field.model"
                      :label="field.label"
                      mask="###"
                      type="tel"
                      suffix="%"
                      autofocus
                    />
                  </div>
                </v-flex>
              </v-layout>
            </v-card-text>
          </v-form>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
export default {
  props: {
    expanded: {
      type: Boolean,
      required: false,
      default: false,
    },
    refreshed: {
      type: Number,
      required: false,
      default: 0,
    },
  },
  data() {
    return {
      loaded: false,
      inputted: false,
      tab: 0,
      candidate: {
        label: '1-5 人',
        value: {
          min: 1,
          max: 5,
        },
      },
      candidates: [
        {
          label: '1-5 人',
          value: {
            min: 1,
            max: 5,
          },
        },
        {
          label: '6-10 人',
          value: {
            min: 6,
            max: 10,
          },
        },
        {
          label: '11-30 人',
          value: {
            min: 11,
            max: 30,
          },
        },
        {
          label: '31 人以上',
          value: {
            min: 31,
            max: 60,
          },
        },
      ],
      fields: [
        {
          name: '性別分布',
          items: [
            {
              model: '',
              label: '男',
            },
            {
              model: '',
              label: '女',
            },
          ],
        },
        {
          name: '學歷分布',
          items: [
            {
              model: '',
              label: '博碩士',
            },
            {
              model: '',
              label: '大學',
            },
            {
              model: '',
              label: '專科',
            },
            {
              model: '',
              label: '高中職',
            },
            {
              model: '',
              label: '國中以下',
            },
          ],
        },
        {
          name: '年齡分布',
          items: [
            {
              model: '',
              label: '20 歲以下',
            },
            {
              model: '',
              label: '21-25 歲',
            },
            {
              model: '',
              label: '26-30 歲',
            },
            {
              model: '',
              label: '31-35 歲',
            },
            {
              model: '',
              label: '36-40 歲',
            },
            {
              model: '',
              label: '41-45 歲',
            },
            {
              model: '',
              label: '46-50 歲',
            },
            {
              model: '',
              label: '51-55 歲',
            },
            {
              model: '',
              label: '56-60 歲',
            },
            {
              model: '',
              label: '60 歲以上',
            },
          ],
        },
        {
          name: '科系分布',
          items: [
            {
              model: '',
              label: '科系一',
            },
            {
              model: '',
              label: '科系二',
            },
            {
              model: '',
              label: '科系三',
            },
            {
              model: '',
              label: '科系四',
            },
            {
              model: '',
              label: '科系五',
            },
            {
              model: '',
              label: '科系六',
            },
            {
              model: '',
              label: '科系七',
            },
            {
              model: '',
              label: '科系八',
            },
            {
              model: '',
              label: '科系九',
            },
            {
              model: '',
              label: '科系十',
            },
          ],
        },
        {
          name: '工作經驗分布',
            items: [
            {
              model: '',
              label: '無工作經驗',
            },
            {
              model: '',
              label: '1 年以下',
            },
            {
              model: '',
              label: '1-3 年',
            },
            {
              model: '',
              label: '3-5 年',
            },
            {
              model: '',
              label: '5-10 年',
            },
            {
              model: '',
              label: '10-15 年',
            },
            {
              model: '',
              label: '15-20 年',
            },
            {
              model: '',
              label: '20-25 年',
            },
            {
              model: '',
              label: '25 年以上',
            },
          ],
        },
      ],
      values: [],
    };
  },
  watch: {
    refreshed(value) {
      this.inputted = false;
      this.$refs.form.reset();
      this.candidate.value = this.candidates[0].value;
      this.tab = 0;
    },
    options(value) {
      this.tab = 0;
    },
    total(value) {
      this.$emit('unlock', value);
    },
    results(value) {
      this.fill(value);
    },
  },
  computed: {
    options() {
      return this.unique(
        this.max(
          this.exist(
            [].concat(
              ...this.values.map((element) => {
                return this.calculate(element, this.candidate.value);
              })
            )
          )
        )
      );
    },
    inputs() {
      return this.values.filter((row) => {
        return row.filter((column) => column).length;
      });
    },
    tabs() {
      return this.options.filter((element) => {
        return element.total >= this.candidate.value.min;
      });
    },
    total() {
      if (!this.values.length || !this.options.length) {
        return 0;
      }
      return this.options[this.tab].total;
    },
    groups() {
      if (!this.total) {
        return [];
      }
      return this.associate(this.fields, this.convert(this.values, {
        total: this.total,
      })).filter((row) => {
        return row.items.filter((column) => {
          return column.value;
        }).length;
      });
    },
    results() {
      return this.fields.map((row) => {
        return row.items.map((column) => {
          return parseInt(column.model || 0, 10) / 100;
        });
      });
    },
  },
  methods: {
    associate(keys, values) {
      return keys.map((key, index) => {
        return {
          name: key.name,
          items: values[index].map((value, index) => {
            return {
              name: key.items[index].label,
              value,
            };
          }),
        };
      });
    },
    convert(array, {
      total = 0,
    } = {}) {
      return array.map((row) => {
        return row.map(column => Math.round(column * total));
      });
    },
    max(array) {
      return array.filter((element) => {
        return element.divisible === Math.max(...array.map((element) => element.divisible));
      });
    },
    unique(array) {
      return array.filter((element, index) => {
        return index === array.findIndex((object) => {
          return JSON.stringify(object) === JSON.stringify(element);
        });
      });
    },
    exist(array) {
      return array.filter((element) => {
        return element.total;
      });
    },
    deviation(array, total) {
      return array.reduce((accumulator, element) => {
        const value = element.value;
        return accumulator + value;
      }, 0) - total;
    },
    range(min, max) {
      return Array.from(
        {
          length: max - min + 1,
        },
        (_, i) => min + i,
      );
    },
    percentage(value, total) {
      return Math.round(value / total * 1000) / 10;
    },
    fill(array) {
      this.inputted = true;
      this.values = array
    },
    calculate(array, {
      min = 1,
      max = 5,
    } = {}) {
      return this.range(min, max).map((index) => {
        const total = array.reduce((accumulator, element) => {
          const value = Math.round(element * index);
          return accumulator + value;
        }, 0);
        const divisible = array.reduce((accumulator, element) => {
          const value = Math.round(element * index % 1 * 10) / 10 % 1 === 0 ? 1 : 0;
          return accumulator + value;
        }, 0);
        const exceed = total > max;
        return {
          total: exceed ? 0 : total,
          divisible: exceed ? 0 : divisible,
        };
      });
    },
  },
};
</script>

<style lang="stylus" scoped>
.default
  cursor default
</style>
