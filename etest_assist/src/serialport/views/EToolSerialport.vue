<template>
  <v-container class="pa-0 fill-height" fluid>
    <!-- <div style="width:100%;height:48px;">
            <e-top-tab :selected_index="selected_index" @select="on_select"></e-top-tab>
        </div> -->
    <div style="width:100%;height: calc(100vh - 32px); min-height:680px;">
      <v-card height="100%" width="100%" tile>
        <div style="display:flex; lex-wrap: nowrap;height:100%">
          <div class="py-2 pl-4 pr-2" style="height:100%;width:15%;width:275px; min-height:600px ">
            <div style="width:100%;height:47%;min-width:250px;">
              <v-card tile :elevation="0" style="width:100%; height:100%;">
                <v-subheader style="height:24px">串口设置</v-subheader>
                <v-divider class="mb-4"></v-divider>
                <v-select :items="serialportarr" :disabled='this._open' v-model="_serial" label="串口号"
                  item-text="comName" item-value="path" dense attach></v-select>
                <div id="error"></div>
                <v-combobox @change="this.values" :items="[110, 300, 600, 1200, 2400, 4800, 9600, 14400, 19200, 38400, 56000, 115200, 128000,
        256000, 'Customize' 
      ]" v-model="_baud" label="波特率" type="number" :disabled='this._open' dense attach></v-combobox>
                <v-select :items="['none', 'odd', 'even', 'mark', 'space']" v-model="_check" :disabled='this._open'
                  label="校验位" dense attach>
                </v-select>
                <v-select :items="[5, 6, 7, 8]" v-model="_data_key" label="数据位" :disabled='this._open' dense attach>
                </v-select>
                <v-select :items="[1, 1.5, 2]" v-model="_stop_key" label="停止位" :disabled='this._open' dense attach>
                </v-select>
                <div style="text-align:center;">

                  <v-btn block small @click="click(1)">
                    <v-icon left :color="_open==true?'red':undefined">mdi-lightbulb</v-icon>
                    {{this._open==true?'关闭':'打开'}}
                  </v-btn>
                </div>
              </v-card>
            </div>
            <div class="mt-1" style="width:100%;height:28%; min-width:250px;">
              <v-card tile :elevation="0" style="width:100%; height:100%;">
                <v-subheader style="height:24px">接受设置</v-subheader>
                <v-divider></v-divider>
                <v-radio-group style="height:15px" dense v-model="_jieshou" row>
                  <v-radio style="height:15px" dense label="ASCII" :value="1"></v-radio>
                  <v-radio style="height:15px" dense label="HEX" :value="2"></v-radio>
                </v-radio-group>
                <v-checkbox style="height:15px" v-model="_rizhi" dense label="接收日志模式显示">
                </v-checkbox>
                <v-checkbox style="height:15px" v-model="_huanhang" dense label="接收完自动换行">
                </v-checkbox>
                <!-- <v-checkbox style="height:15px" v-model="receive_checkbox3" dense label="接收转向至文件">
                                </v-checkbox> -->
                <v-checkbox style="height:15px" v-model="_zanting" dense label="暂停接收区显示">
                </v-checkbox>
                <!-- <div class="my-4">
                                    <v-btn block small>其他选项</v-btn>
                                </div>
                                <div class="my-4">
                                    <v-btn block small>清除接收</v-btn>
                                </div> -->
              </v-card>
            </div>
            <div class="mt-1" style="width:100%;height:25%; min-width:250px;">
              <v-card tile :elevation="0" style="width:100%; height:100%;">
                <v-subheader style="height:24px">发送设置</v-subheader>
                <v-divider></v-divider>
                <v-radio-group style="height:15px" dense v-model="_fasong" @change="changefasongleixing" row>
                  <v-radio style="height:15px" dense label="ASCII" :value="1"></v-radio>
                  <v-radio style="height:15px" dense label="HEX" :value="2"></v-radio>
                </v-radio-group>
                <!-- <v-checkbox style="height:15px" v-model="send_checkbox1" dense label="自动解析转义符">
                                </v-checkbox>
                                <v-checkbox style="height:15px" v-model="send_checkbox2" dense label="AT指令自动回车">
                                </v-checkbox>
                                <v-checkbox style="height:15px" v-model="send_checkbox3" dense label="自动发送校验位">
                                </v-checkbox>
                                <v-checkbox style="height:15px" v-model="send_checkbox4" dense label="打开文件数据源">
                                </v-checkbox> -->
                <v-row class="px-3" align="center">
                  <v-checkbox v-model="_xunhuan" @change="closeSetInitval" :label="`循环周期:`" hide-details
                    class="shrink mr-2 mt-0"></v-checkbox>
                  <v-text-field style="width:80px;" v-model="_ms" :disabled="!_xunhuan"></v-text-field>ms

                </v-row>
              </v-card>
            </div>
          </div>



          <div class="py-2 pl-2 pr-8 " style="height:100%;width:84%;min-height:650px min-width:450px">
            <div style="width:100%;height:72%;min-height:520px">
              <v-card tile :elevation="0" style="width:100%; height:100%;">
                <v-subheader style="height:24px">数据日志</v-subheader>
                <v-divider></v-divider>
                <!-- <v-sheet width="100%" height="90%" class="pa-0 ma-1 mb-1" style="border: 2px solid grey"></v-sheet> -->
                <v-sheet width="100%" class="pa-0 ma-0 mb-1" @keydown.stop
                  style="height: calc(67vh - 52px);min-height:490px; ">
                  <e-script-editor id="js_data" :script="js_data" type="jsdata" @change="on_change" />
                </v-sheet>
              </v-card>
            </div>

            <div class="mt-2" style="width:100%;height:27%;min-width:450px">
              <v-card tile :elevation="0" style="width:100%; height:100%;">
                <v-row class="py-0">
                  <v-col class="pa-0" cols="9">
                    <v-subheader style="height:44px">数据发送</v-subheader>
                  </v-col>
                  <v-col style='text-align:right' cols="3">
                    <v-row class="py-0">
                      <!-- v-if="_bind == true" -->
                      <v-col class="pa-0 " cols="6" style="text-align:center;">
                        <v-btn small @click="closefs">
                          <v-icon left>mdi-arrow-bottom-left</v-icon>清除
                        </v-btn>
                      </v-col>
                      <v-col class="pa-0 " cols="6" style="text-align:center;">
                        <v-btn small @click="closejs">
                          <v-icon left>mdi-arrow-top-left</v-icon>清除
                        </v-btn>
                      </v-col>

                    </v-row>
                  </v-col>
                </v-row>
                <v-divider></v-divider>

                <v-row class="pt-4 mt-2">
                  <v-col class="pa-0  pl-3 pr-2" cols="10">
                    <v-sheet width="100%" class="pa-0 ma-0 mb-1" @keydown.stop
                      style="height: calc(11vh);min-height:85px;">
                      <e-script-editor id="push_data" :script="push_data" type="push" @change="on_change" />
                    </v-sheet>
                  </v-col>
                  <v-col class="pa-0  pr-3" cols="2">
                    <v-btn class="ma-0 px-0 " style="height:calc(11vh);min-height:85px;" block @click="write"
                      :disabled="!_open" outlined>{{this._zidong==false?'发送':'停止发送'}}
                    </v-btn>
                  </v-col>
                </v-row>
              </v-card>
            </div>
          </div>
        </div>
      </v-card>
    </div>
  </v-container>
</template>

<script>
  import EScriptEditor from "../components/widgets/EDataFormatEditor";
  var encoding = require('encoding')
  const serialport = window.require('serialport');
  var port = undefined
  export default {
    components: {
      "e-script-editor": EScriptEditor,
    },


    computed: {
      _baud: {
        get: function () {
          return this.$store.state.serialport.items.baud
        },
        set: function (v) {
          return this.$store.commit('serialport/baud', v)
        }
      },
      _serial: {
        get: function () {
          return this.$store.state.serialport.items.serial
        },
        set: function (v) {
          return this.$store.commit('serialport/serial', v)
        }
      },

      _check: {
        get: function () {
          return this.$store.state.serialport.items.check
        },
        set: function (v) {
          return this.$store.commit('serialport/check', v)
        }
      },
      _data_key: {
        get: function () {
          return this.$store.state.serialport.items.data_key
        },
        set: function (v) {
          return this.$store.commit('serialport/data_key', v)
        }
      },
      _stop_key: {
        get: function () {
          return this.$store.state.serialport.items.stop_key
        },
        set: function (v) {
          return this.$store.commit('serialport/stop_key', v)
        }
      },
      _jieshou: {
        get: function () {
          return this.$store.state.serialport.items.jieshou
        },
        set: function (v) {
          return this.$store.commit('serialport/jieshou', v)
        }
      },
      _fasong: {
        get: function () {
          return this.$store.state.serialport.items.fasong
        },
        set: function (v) {
          return this.$store.commit('serialport/fasong', v)
        }
      },
      _rizhi: {
        get: function () {
          return this.$store.state.serialport.items.rizhi
        },
        set: function (v) {
          return this.$store.commit('serialport/rizhi', v)
        }
      },
      _huanhang: {
        get: function () {
          return this.$store.state.serialport.items.huanhang
        },
        set: function (v) {
          return this.$store.commit('serialport/huanhang', v)
        }
      },
      _zanting: {
        get: function () {
          return this.$store.state.serialport.items.zanting
        },
        set: function (v) {
          return this.$store.commit('serialport/zanting', v)
        }
      },
      _xunhuan: {
        get: function () {
          return this.$store.state.serialport.items.xunhuan
        },
        set: function (v) {
          return this.$store.commit('serialport/xunhuan', v)
        }
      },
      _ms: {
        get: function () {
          return this.$store.state.serialport.items.ms
        },
        set: function (v) {
          return this.$store.commit('serialport/ms', v)
        }
      },
      serialportarr: {
        get: function () {
          return this.$store.state.serialport.items.serialportarr
        },
        set: function (v) {
          return this.$store.commit('serialport/serialportarr', v)
        }
      },
      _open: {
        get: function () {
          return this.$store.state.serialport.items.open
        },
        set: function (v) {
          return this.$store.commit('serialport/open', v)
        }

      },
      push_data: {
        get: function () {
          return this.$store.state.serialport.items.push;
        },
        set: function (v) {
          return this.$store.commit("serialport/push", v)
        },
      },
      _zidong: {
        get: function () {
          return this.$store.state.serialport.items.zidong;
        },
        set: function (v) {
          return this.$store.commit("serialport/zidong", v)
        },
      },
      js_data: {
        get: function () {
          return this.$store.state.serialport.items.jsdata;
        },
        set: function (v) {
          // this.js_data = v; 
          return this.$store.commit("serialport/jsdata", v)
        },
      },
    },

    data: () => ({
      zidongfasong: undefined,
    }),



    mounted: function () {
      serialport.list().then(
        ports => {
          //ports 串口
          this.serialportarr = ports
          this._serial = this._serial === undefined ? ports[0].comName : this._serial
        }
      )
    },

    beforeMount: async function () {
      let db_items = await this.$store.dispatch("db_list", {
        kind: "serialport",
      });
      if (db_items.length > 0) {
        this.$store.commit("serialport/setItem", db_items);
      }
      this.load_data()
    },


    beforeDestroy: async function () {
      // clearInterval(this.zidongfasong)
      // this.zidongfasong = undefined
      let _this = this
      port.close(function () {
        console.log('端口已关闭')
        port = undefined
        _this._open = false
        if (_this._zidong == true) {
          _this._zidong = false
          clearInterval(_this.zidongfasong)
          _this.zidongfasong = undefined
        }
      });
      let state_items = this.$store.state.serialport.items.items;
      let len = state_items.length;
      for (let index = 0; index < len; index++) {
        let item = state_items[index];
        item.open = false
        item.zidong = false
        item.id = index;
        await this.$store.dispatch("db_update", {
          kind: "serialport",
          doc: item,
        });
      }
    },

    watch: {
      selected_index: function () {
        this.load_data();
      }
    },




    methods: {

      load_data() {
        let o = this.$store.state.serialport.items;
        this._serial = o.serial;
        this._baud = o.baud;
        this._check = o.check;
        this._data_key = o.data_key;
        this._stop_key = o.stop_key;
        this._jieshou = o.jieshou;
        this._fasong = o.fasong;
        this._rizhi = o.rizhi;
        this._huanhang = o.huanhang;
        this._zanting = o.zanting;
        this._xunhuan = o.xunhuan;
        this._ms = o.ms;
        this._open = o.open;
        this.push_data = o.push;
        this._zidong = o.zidong;
        this.js_data = o.jsdata;
      },


      on_change(id, script) {
        this[id] = script
      },



      closeSetInitval: function (item) {
        if (item == false) {
          this._zidong = false
          clearInterval(this.zidongfasong)
          this.zidongfasong = undefined
        }
      },



      click: function () {
        if (!port) {
          port = new serialport(this._serial, {
            baudRate: this._baud, //波特率
            dataBits: this._data_key, //数据位
            parity: this._check, //校验
            stopBits: this._stop_key, //停止位
            flowControl: false,
            autoOpen: false //不自动打开
          }, false)
          this.open()

        } else {
          if (port) {
            var _this = this
            port.close(function () {
              console.log('端口已关闭')
              port = undefined
              _this._open = false
              if (_this._zidong == true) {
                _this._zidong = false
                clearInterval(_this.zidongfasong)
                _this.zidongfasong = undefined
              }
            });
          }
        }
      },



      open: function () {
        var _this = this
        port.on('error', (error) => {
          console.log('Error: ', error.message);
        })
        port.open(function (error) {
          if (error) {
            console.log();
            _this.$store.commit("setMsgError", "打开端口" + _this._serial + "错误：" + error);
          } else {
            console.log(port)
            _this._open = true
            _this.getck()
          }
          port.on('data', function (data) {
            
            if (_this._jieshou == 1) {
              let BuffMsg = Buffer.from(data, 'hex')
              BuffMsg = encoding.convert(BuffMsg, "UTF8", "GBK").toString()
              _this.showdata(BuffMsg)
            } else {
              let BuffMsg = data.toString('hex')
              _this.showdata(BuffMsg)
            }
          });
        });

      },


      write: function () {
        //

        var SendBuff
        if (this.push_data !== '') {
          if (this._fasong == 1) {
            try {
              SendBuff = encoding.convert(this.push_data, "GBK")
            } catch (error) {
              this.$store.commit("setMsgError", error);
            }
          } else {
            try {
              SendBuff = Buffer.from(this.push_data.replace(/\s*/g, ""), 'hex')
            } catch (error) {
              this.$store.commit("setMsgError", error);
            }
          }
          if (this._xunhuan == false) {
            port.write(SendBuff);
            this.showfasong()
          } else {
            this.zdfs(SendBuff)
          }
        } else {
          this.$store.commit("setMsgError", '无法发送空数据');
        }
      },


      zdfs: function (SendBuff) {
        var _this = this
        if (this._zidong == true) {
          this._zidong = false
          clearInterval(this.zidongfasong)
          this.zidongfasong = undefined
        } else {
          this.zidongfasong = setInterval(function () {
            _this._zidong = true
            port.write(SendBuff);
            _this.showfasong()
          }, _this._ms);
        }
      },



      showfasong: function () {
        if (this._rizhi == true) {
          this.js_data = this.js_data + `\n 发送:\n ${this.push_data} \n`
        }
      },


      showdata: function (data) {
        if (this._zanting == false) {
          // this.js_data = this.js_data + `\n 收到 : \n ${data} \n`
          if (this._rizhi == false && this._huanhang == false) {
            this.js_data = this.js_data + `${data}`
          } else if (this._rizhi == true && this._huanhang == false) {
            this.js_data = this.js_data + `\n 收到: \n ${data}`
          } else if (this._rizhi == false && this._huanhang == true) {
            this.js_data = this.js_data + `\n ${data}`
          } else if (this._rizhi == true && this._huanhang == true) {
            this.js_data = this.js_data + `\n 收到: \n ${data} \n`
          }
        }
      },


      getck: function () {
        port.set({
          cts: true,
          dsr: false,
          dtr: true,
          rts: true,
          brk: false
        })
        port.get(function (err, data) {
          console.log(err)
          console.log(data)
        })
      },



      changefasongleixing: function (e) {
        if (this.push_data != '') {
          if (e == 2) {
            let SendBuff = encoding.convert(this.push_data, "GBK")
            this.push_data = SendBuff.toString('hex')
          } else if (e == 1) {
            let SendBuff = Buffer.from(this.push_data.replace(/\s*/g, ""), 'hex')
            SendBuff = Buffer.from(SendBuff, 'hex')
            this.push_data = encoding.convert(SendBuff, "UTF8", "GBK").toString()
          }
        }
      },



      closefs: function () {
        this.push_data = ""
        // this.$refs.child1.jump()
      },


      closejs: function () {
        this.js_data = ""
        // this.$refs.child1.$emit('childClick','我是父组件')
      },
      values: function (v) {
        console.log(v)
        this._baud = Number(v)
      }
    }

  }
</script>