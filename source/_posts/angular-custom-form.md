---
title: angular自定义表单组件
date: 2020-07-02 17:25:41
excerpt: angular自定义表单组件
tags:
position:
image_url:
---
## 自定义表单组件可以分为两种：
1、组件中有input、areatext、select等原生可设置formctrol组件。以下简称：【有原生组件】
2、组件中没有以上组件。以下简称：【无原生组件】


针对于以上两种类型组件，有相应两种方式形成组件。
## 【有原生组件】
>注意：父组件中使用子组件   需要加ngDefaultControl属性，否则会报错：no value accessor for form control with name

```
# 父组件
<hc-form-mobile 
    ngDefaultControl 
    [parentForm]="form" nz-col 
    [nzSm]="styleInfo.controlGridNum" 
    formControlName="phone" 
    [styleInfo]="styleInfo"></hc-form-mobile>
```

```
# 子组件  对应html中form属性也要设置未parentForm 
export class FormMobileComponent implements OnInit {

  @Input() parentForm :FormGroup;//从父组件传来的父组件formgroup
  @Input() styleInfo :FormLayoutStyleInterface;

  constructor(
    public i18n: I18NService,
  ) {}

  ngOnInit(): void {//设置检验规则
    this.phone.setValidators([Validators.required, Validators.pattern(/^1\d{10}$/)]);
  }

  get phone() {
    return this.parentForm.controls.phone;
  }

  set phone(data) {
    this.parentForm.controls.phone.setValue(data);
  }

}
```
## 【无原生组件】
```
# 子组件  注意看【重点】
@Component({
  selector: 'hc-form-department',
  templateUrl: './form-department.component.html',
  styles: [
  ],
  //【重点1】
  providers: [{
    provide: NG_VALUE_ACCESSOR,
    useExisting: FormDepartmentComponent,
    multi: true
  }]
})
//【重点2】，实现接口ControlValueAccessor 
export class FormDepartmentComponent implements OnInit,ControlValueAccessor {

  form: FormGroup;
  formChangeFn;

  departmentList:[{
    deptNum:'',
    deptName:''
  }];

  nodes = [];

  constructor(
    public i18n: I18NService,
    private fb: FormBuilder,
    private http: HttpClientService,
    private convertTree: ConvertTreeService,
    private modal: ModalHelper
  ) {
    this.form = fb.group({
      deptNum: [null],
    });
  }

  ngOnInit(): void {
    this.getAllDepartment();
  }

  /**
   * 新建部门
   */
  addDepartment(){
    this.modal.createStatic(UserDepartmentEditComponent,
      {record: {isEdit: false, key: 0}},
      { size: 'md'}
    ).subscribe((res) => {
      this.getAllDepartment();
    });
  }

  /**
   * 获取全部部门列表
   */
  getAllDepartment(){}

  onChange(data){
    this.formChangeFn(data);
  }
  //【重点3】实现方法registerOnChange、registerOnTouched、writeValue
  registerOnChange(fn: any): void {
    this.formChangeFn = fn;
  }

  registerOnTouched(fn: any): void {
  }

  writeValue(value: any): void {
    if(value){
      this.deptNum = value;
    }
  }

  get deptNum() {
    return this.form.controls.deptNum;
  }

  set deptNum(data) {
    this.form.controls.deptNum.setValue(data);
  }
  //【重点4】这是自定义的函数，为了在父组件提交时检测子组件的值并提示。
  checkValidity() {
    this.deptNum.markAsDirty();
    this.deptNum.updateValueAndValidity();
  }

}
```
