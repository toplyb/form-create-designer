<template>
    <form-create class="_fc-validate" :rule="rule" :option="option" :value="formValue"
                 @update:value="onInput"></form-create>
</template>

<script>
export default {
    name: 'Validate',
    props: {
        value: Array
    },
    watch: {
        value(n) {
            this.formValue = this.parseValue(n);
        }
    },
    data() {
        return {
            formValue: {},
            option: {
                form: {
                    labelPosition: 'top',
                    size: 'mini',
                    labelWidth: '90px'
                },
                submitBtn: false,
                formData: this.parseValue(this.value)
            },
            rule: [
                {
                    type: 'select',
                    field: 'type',
                    value: undefined,
                    title: '是否必填',
                    options: [
                        {value: undefined, label: '否'},
                        {value: 'string', label: '必填'}
                    ],
                    control: [
                        {
                            handle: v => {
                                return !!v;
                            },
                            rule: [
                                {
                                    type: 'group',
                                    field: 'validate',
                                    props: {
                                        expand: 1,
                                        rules: [
                                            {
                                                type: 'select',
                                                title: '触发方式',
                                                field: 'trigger',
                                                value: 'blur',
                                                options: [
                                                    {label: 'blur', value: 'blur'},
                                                ]
                                            },
                                            {
                                                type: 'select',
                                                title: '验证方式',
                                                field: 'mode',
                                                options: [
                                                    {value: 'required', label: '必填'}
                                                ],
                                                value: 'required',
                                                control: [
                                                    {
                                                        value: 'required',
                                                        rule: [
                                                            {
                                                                type: 'hidden',
                                                                field: 'required',
                                                                value: true
                                                            }
                                                        ]
                                                    }
                                                ]
                                            }
                                        ]
                                    },
                                    value: []
                                }
                            ]
                        }
                    ]
                },

            ]
        };
    },
    methods: {
        onInput: function (formData) {
            let val = [];
            const {validate, type} = formData;
            if (type && !validate) {
                return;
            } else if (type) {
                validate.forEach(v => {
                    v.type = type;
                });
                val = [...validate];
            }
            this.$emit('input', val);
        },
        parseValue(n) {
            let val = {
                validate: n ? [...n] : [],
                type: n.length ? n[0].type : undefined
            };
            val.validate.forEach(v => {
                if (!v.mode) {
                    Object.keys(v).forEach(k => {
                        if (['message', 'type', 'trigger', 'mode'].indexOf(k) < 0) {
                            v.mode = k;
                        }
                    });
                }
            });

            return val;
        }
    }
};
</script>

<style>
._fc-validate .form-create .el-form-item {
    margin-bottom: 22px !important;
}
</style>
