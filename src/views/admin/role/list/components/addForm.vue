<template>
  <v-dialog v-model="dialog" width="600" persistent>
    <v-card ref="row">
      <v-card-text>
        <v-form ref="form" lazy-validation>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field
                  v-model="form.name"
                  label="名称*"
                  :rules="nameRules"
                  type="text"
                  required
                ></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field
                  v-model="form.value"
                  label="value*"
                  :rules="valueRules"
                  type="text"
                  required
                ></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field
                  v-model="form.description"
                  label="描述"
                  type="text"
                ></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-form>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn outlined color="darken-1" @click="handleCancel">取消</v-btn>
        <v-btn
          outlined
          :loading="loading"
          color="primary darken-1"
          @click="handleSubmit"
          >提交</v-btn
        >
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import { validateValue, validateUsername } from "@/util/validate";
import { addRole } from "@/api/role";

export default {
  name: "Add",
  props: {
    alert: {
      type: Boolean,
      default: function() {
        return {};
      }
    }
  },
  data: () => ({
    name: "add",
    loading: false,
    form: {},
    nameRules: [
      v => !!v || "名称不能为空!",
      v => validateUsername(v) || "必须是中文、英文、数字包括下划线"
    ],
    valueRules: [
      v => !!v || "VAULE不能为空!",
      v => validateValue(v) || "必须是大写英文、下划线"
    ]
  }),
  computed: {
    dialog: {
      get: function() {
        return this.alert;
      },
      set: function() {}
    }
  },
  methods: {
    handleCancel() {
      this.$emit("handleCancel");
    },
    handleSubmit() {
      this.loading = true;
      // 开始提交
      if (this.$refs.form.validate()) {
        addRole(this.form)
          .then(res => {
            this.loading = false;
            if (res.code === "200" && res.data) {
              this.$swal.fire({
                text: "添加成功",
                type: "success",
                toast: true,
                position: "top",
                showConfirmButton: false,
                timer: 3000
              });
              this.$parent.refresh();
              this.$emit("handleCancel");
            } else {
              this.$swal.fire({
                text: res.message,
                type: "error",
                toast: true,
                position: "top",
                showConfirmButton: false,
                timer: 3000
              });
            }
          })
          .catch(e => {
            this.loading = false;
            this.$swal.fire({
              text: e.message,
              type: "error",
              toast: true,
              position: "top",
              showConfirmButton: false,
              timer: 3000
            });
          });
      }
      this.loading = false;
    }
  }
};
</script>
