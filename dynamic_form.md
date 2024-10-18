## 数据结构
```ts
type FormData = { [index: string]: any };
type Fn = (data: FormData) => string | void;
type ElType = "Radio" | "Checkbox" | "Input" | "Custom"; // ...

type Action = "clcik";

type Operate = {
  getForm: (key: string) => any;
  setForm: (key: string, val: any) => void;
  getField: (key: string) => any;
  setField: (key: string, option: string, val: any) => any;
  trigger: (field: Field, action: Action, data: any) => any;
};

interface Form {
  config: Field[];
  data: FormData;
  check: {
    confirm: Fn[];
    verify: Fn[];
  };
}

interface Field {
  key?: string;

  region: string;
  field: string;
  type: ElType;
  name: string;
  desc?: string;
  style?: string;
  disabled?: boolean;

  rules?: {
    required?: boolean;
    max?: number;
    min?: number;
    regex?: string;
  };

  actions?: Record<Action, (val: any, field: Field, op: Operate) => void>;
  subscription?: {
    [field: string]: (val: any, field: Field, op: Operate) => void;
  };
}

```
## 流程
```mermaid
flowchart TD
    subgraph 渲染表单
    A[开始] --> B[接口获取配置]
    B --> C[字段解析收集]
    C --> D[字段转换]
    D --> E[根据配置生成页面]
    E --> F{是否需要自定义组件?}
    F -->|是| G[加载自定义组件]
    G --> H[渲染表单]
    F -->|否| H
    end

    subgraph 用户交互
    H --> I[用户输入/选择]
    I --> J{是否需要联合校验?}
    J -->|是| K[执行联合校验]
    K --> L{校验通过?}
    L -->|否| I
    J -->|否| M{是否需要字段联动?}
    L -->|是| M
    M -->|是| N[执行字段联动]
    N --> O[更新表单]
    O --> I
    M -->|否| I
    end

    subgraph 提交表单
    I --> P{表单是否完成?}
    P -->|否| I
    P -->|是| Q[验证所有字段]
    Q --> R{全部验证通过?}
    R -->|否| I
    R -->|是| S[提交表单数据]
    S --> T[结束]
    end
```