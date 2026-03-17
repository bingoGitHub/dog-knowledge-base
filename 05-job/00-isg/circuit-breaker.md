
```
//熔断规则
export class CircuitBreakerRule {
	assetId: string        //接口服务ID
    windowStatTime: number //统计窗口时长，毫秒为单位
	exceptionRate: number  //异常比例，负数为关闭，非负数为开启
    slowCallRspTime: number //慢调用响应时间，单位：毫秒
    slowCallRate: number       //慢调用比例，负数为关闭，非负数为开启
    circuitBreakerTime: number //熔断时长，单位：毫秒
    requestMinCnt: number      //最小请求数
	slowCallEnable: boolean   //慢调用开关
	exceptionEnable: boolean  //异常比例开关
}
```
熔断规则用于保护指定资产（`assetId`），在统计窗口（`windowStatTime`）内请求数达到最小请求数（`requestMinCnt`）后，若异常比例或慢调用比例超过阈值（`exceptionRate`/`slowCallRate`）且对应开关（`exceptionEnable`/`slowCallEnable`）开启，则触发熔断，持续 `circuitBreakerTime` 时长，期间所有请求快速失败，避免下游故障蔓延。