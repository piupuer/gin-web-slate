# Finite state machine

Do you often encounter finite state machines or approval workflow in your development process?  
Gin Web provides a simple FSM implementation example: Leave request.

## Config machine

You can add a new machine easily:
<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/config.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/config.jpeg?raw=true" width="600" />
</a>

## Show results

### Approval refused

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/refused.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/refused.jpeg?raw=true" width="600" />
</a>

### Approval approved or ended

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/ended.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/ended.jpeg?raw=true" width="600" />
</a>

### Approval cancelled

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/cancelled.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/cancelled.jpeg?raw=true" width="600" />
</a>

## How to use

You can customize your workflow is like leave request similar to the following functions:

### Transition

> Leave Transition:

```go
package service

func (my MysqlService) LeaveTransition(logs ...resp.FsmApprovalLog) (err error) {
  m := make(map[uint][]string)
  for _, log := range logs {
    if log.Category == global.FsmCategoryLeave {
      if log.Resubmit == constant.One {
        arr := make([]string, 0)
        if item, ok := m[models.LevelStatusWaitingConfirm]; ok {
          arr = item
        }
        m[models.LevelStatusRefused] = append(arr, log.Uuid)
      } else if log.Cancel == constant.One {
        arr := make([]string, 0)
        if item, ok := m[models.LevelStatusCancelled]; ok {
          arr = item
        }
        m[models.LevelStatusCancelled] = append(arr, log.Uuid)
      } else if log.Confirm == constant.One {
        arr := make([]string, 0)
        if item, ok := m[models.LevelStatusWaitingConfirm]; ok {
          arr = item
        }
        m[models.LevelStatusWaitingConfirm] = append(arr, log.Uuid)
      } else if log.End == constant.One {
        arr := make([]string, 0)
        if item, ok := m[models.LevelStatusApproved]; ok {
          arr = item
        }
        m[models.LevelStatusApproved] = append(arr, log.Uuid)
      } else {
        arr := make([]string, 0)
        if item, ok := m[models.LevelStatusApproving]; ok {
          arr = item
        }
        m[models.LevelStatusApproving] = append(arr, log.Uuid)
      }
    }
  }
  for status, uuids := range m {
    err = my.Q.Tx.
      Model(&models.Leave{}).
      Where("fsm_uuid IN (?)", uuids).
      Update("status", status).Error
    if err != nil {
      return errors.WithStack(err)
    }
  }
  return nil
}
```

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/transition.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/transition.jpeg?raw=true" width="600" />
</a>

### Get Detail

> Get Leave Fsm Detail:

```go
package service

func (my MysqlService) GetLeaveFsmDetail(detail req.FsmSubmitterDetail) []resp.FsmSubmitterDetail {
  arr := make([]resp.FsmSubmitterDetail, 0)
  switch uint(detail.Category) {
  case global.FsmCategoryLeave:
    var leave models.Leave
    my.Q.Tx.
      Model(&models.Leave{}).
      Where("fsm_uuid = ?", detail.Uuid).
      First(&leave)
    if leave.Id > 0 {
      arr = append(arr, resp.FsmSubmitterDetail{
        Name: "leave desc",
        Key:  "desc",
        Val:  leave.Desc,
      })
      if !leave.StartTime.IsZero() {
        arr = append(arr, resp.FsmSubmitterDetail{
          Name: "leave start time",
          Key:  "startTime",
          Val:  leave.StartTime.String(),
        })
      }
      if !leave.EndTime.IsZero() {
        arr = append(arr, resp.FsmSubmitterDetail{
          Name: "leave end time",
          Key:  "endTime",
          Val:  leave.EndTime.String(),
        })
      }
    }
  }
  return arr
}
```

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/get_detail.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/get_detail.jpeg?raw=true" width="600" />
</a>

### Update Detail

> Update Leave Fsm Detail:

```go
package service

func (my MysqlService) UpdateLeaveFsmDetail(detail req.UpdateFsmSubmitterDetail) (err error) {
  switch uint(detail.Category) {
  case global.FsmCategoryLeave:
    detail.Parse()
    m := make(map[string]interface{})
    for i, key := range detail.Keys {
      m[utils.SnakeCase(key)] = detail.Vals[i]
    }
    var leave models.Leave
    q := my.Q.Tx.
      Model(&models.Leave{}).
      Where("fsm_uuid = ?", detail.Uuid)
    q.First(&leave)
    if leave.Id > 0 {
      err = q.Updates(&m).Error
      return errors.WithStack(err)
    }
  }
  return nil
}
```

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/update_detail.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/update_detail.jpeg?raw=true" width="600" />
</a>

### Model

> Leave Model:

```go
package models

const (
  LevelStatusWaiting        uint = iota // waiting approval
  LevelStatusApproved                   // approved
  LevelStatusRefused                    // approval rejection
  LevelStatusCancelled                  // approval cancelled
  LevelStatusApproving                  // pending approval but not end
  LevelStatusWaitingConfirm             // approval waiting confirm
)

type Leave struct {
  ms.M
  UserId          uint                    `gorm:"comment:'user id(SysUser.Id)'" json:"userId"`
  User            SysUser                 `gorm:"foreignKey:UserId" json:"user"`
  FsmUuid         string                  `gorm:"size:100;comment:'finite state machine uuid'" json:"fsmUuid"`
  Status          uint                    `gorm:"default:0;comment:'status(0:submitted 1:approved 2:refused 3:cancel 4:approving 5:waiting confirm)'" json:"status"`
  ApprovalOpinion string                  `gorm:"comment:'approval opinion or remark'" json:"approvalOpinion"`
  Desc            string                  `gorm:"comment:'submitter description'" json:"desc"`
  StartTime       carbon.ToDateTimeString `gorm:"comment:'start time'" json:"startTime"`
  EndTime         carbon.ToDateTimeString `gorm:"comment:'end time'" json:"endTime"`
}
```

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/model.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/model.jpeg?raw=true" width="600" />
</a>

### Common Api

> Fsm Common Api:

```go
package router

func (rt Router) Fsm() {
  router1 := rt.Casbin("/fsm")
  router2 := rt.CasbinAndIdempotence("/fsm")
  router1.GET("/approving/list", v1.FindFsmApprovingLog(rt.ops.v1Ops...))
  router1.GET("/log/track", v1.FindFsmLogTrack(rt.ops.v1Ops...))
  router1.GET("/submitter/detail", v1.GetFsmSubmitterDetail(rt.ops.v1Ops...))
  router1.PATCH("/submitter/detail", v1.UpdateFsmSubmitterDetail(rt.ops.v1Ops...))
  router1.PATCH("/approve", v1.FsmApproveLog(rt.ops.v1Ops...))
  router1.PATCH("/cancel", v1.FsmCancelLogByUuids(rt.ops.v1Ops...))
}
```

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/fsm.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/fsm/fsm.jpeg?raw=true" width="600" />
</a>

