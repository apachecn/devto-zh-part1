# React + Redux 容器模式

> 原文：<https://dev.to/sophiedebenedetto/the-react--redux-container-pattern-bmk>

*注:这篇文章最初发表在我的博客上，[伟大的代码冒险](http://www.thegreatcodeadventure.com/)*

### 干净利落地构建响应式应用是很难的

我在 React 中构建的第一个大型(好吧，更像是中型)应用程序非常简单。这是一个简单的 CRUD 应用程序，用于管理你自己的猫及其相关爱好的列表(非常有趣)。如此简单，保持我的代码整洁有序并不太难。没有太多花哨的功能，所以我的容器组件系统获取数据并将其提供给表示组件感觉很自然。

我对这种模式的理解在大约一年前被打破了，当时我在熨斗学校当老师，我们带领 26 名学生通过一个 React 项目 sprint，在这个项目中，他们分成小组来开发他们自己的各种复杂的 React + Redux 应用程序。这就是事情变得混乱的地方。管理这样一个多样化的项目组是一个很好的方式，可以同时遇到所有的 bug 和所有的困难设计决策，所有的 T2 和 T4。

尽管这可能很令人兴奋，但它确实让 React 中容器模式的实用性和优雅性得到了充分体现。我们希望实现一种符合单一责任原则的模式，并保持我们的代码干燥，而不是允许任何和所有组件获取和操作数据，这会使调试变得非常糟糕。

因此，我想我应该更深入地研究容器模式，并给出一个实现示例。但是在我们进入代码之前，让我们先讨论一下容器和表示组件。

#### 什么是容器组件？

[![](img/374ee29a76498f4554d067340f897836.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fgCIpQjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.thegreatcodeadventure.com/conteimg/2016/11/6234816796_6401dd69b0_b.jpg)

在阅读容器组件时，我经常遇到这样一句话:

> “容器组件是知道 Redux 的组件”
> –*互联网*

那是什么意思？

嗯，容器组件是一个负责检索数据的组件，为了获取数据，该组件需要使用 Redux 的`connect`和`mapStateToProps`函数。

容器组件将通过`mapStateToProps`从状态中获取数据。然后，组件将把数据的必要部分作为`props`向下传递给其子组件。

容器组件还负责调度对应用程序状态进行更改的操作。

我经常遇到的另一个短语是“控制器视图”和“视图”之间的区别。这个类比对我来说很有意义，来自 Rails。如果 React 是一种视图层技术，一些视图仍然负责检索数据(*控制器视图*)并将数据传递给其他视图以便显示(*表示视图*)。

#### 什么是表象成分？

如果容器组件实际上利用 Redux 来获取数据，那么表示性组件只是从其父容器接收数据并显示它。

因此，您可能想知道，如果表示组件只是显示数据，而容器组件是包含任何动作触发功能的组件，那么用户与表示组件的交互如何最终触发动作呢？

这就是回调道具的用武之地。

#### 回调函数作为道具

在我们接下来的例子中，我们将看到如何在容器组件中定义一个分派动作的函数。响应于用户的交互，这样的函数将作为道具传递给子组件、表示组件，并通过回调来触发。

好了，现在我们*差不多*准备好深入代码了。

### 应用背景

我们将看到的代码来自一个学生出勤跟踪应用程序，该应用程序允许学生登录并表明他们当天已经到校。教师可以通过颜色编码的日历登录并查看他们班级的出勤记录，从学生列表中单击日历日和学生姓名，查看学生出勤记录的详细信息。

我们将进一步了解教师方面的事情，实现容器模式来构建教师选择日历日和学生查看该学生当天出勤记录详细信息的能力。

大概是这样的:

[![](img/ec540b1517a8e1336a2dc79d55fa9a67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xpFOrVLb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://www.thegreatcodeadventure.com/conteimg/2016/11/calendar-demo.gif)

我们开始吧！

#### 组件设计

当在 React 中构建时，我发现做很多很多的线框真的很有帮助。因此，在深入研究代码之前，让我们先来讨论一下组件的整体结构。

正如我们从上图中看到的，我们有几个不同的领域非常适合组件化。这幅图像可以分成三个不同的部分。

*   日历
*   学生名单
*   出勤记录显示

所以，我们将构建一个容器组件，`ScheduleContainer`，它包含了日历和考勤记录显示的子组件。我们将制作一个由`ScheduleContainer`呈现的`StudentsContainer`组件，但它又会呈现一个表示性组件`StudentList`。

大概是这样的:

[![](img/730b82e4532c26a26f39550d27081d2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---UBbyTO3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.thegreatcodeadventure.com/conteimg/2016/11/calendar-redux.png)

为了显示出勤记录的详细信息，我们需要知道所选的学生是谁以及所选的日期。有了这些信息，我们就可以在应用程序的状态中找到考勤记录，确定正确的考勤记录，并将其传递给考勤记录显示组件进行显示。

在我们担心选择学生和动态呈现正确的出勤记录之前，我们将很好地显示所有数据。然后，我们将继续使用回调函数从`studentList`组件中选择学生，以更改`ScheduleContainer`传递给`attendanceRecordShow`显示的出勤记录。

#### 步骤 1:`connect`-调用我们的容器组件并获取数据

首先，我们将设置最顶层的容器组件`ScheduleContainer`，并让它从 state 访问所需的数据。

这篇文章与事物的“后端”无关，所以我们不会真的深入到动作创建者函数或减少者。我们假设 state 中的数据如下:

```
{
  attendanceRecords: [
    {id: 1, date: '10-7-2017', records: [
      {id: 1, student_id: 7, arrived: true, arrivedAt:   
       '10am'}, 
      {id: 2, student_id: 8, arrived: false, arrivedAt:   
       null}]},
    {id: 2, date: '10-8-2017', records: [
      {id: 3, student_id: 7, arrived: true, arrivedAt:   
       '10:20am'}, 
      {id: 2, student_id: 8, arrived: true, arrivedAt:   
       '9:00am'},]},
  ],
  students: [
    {id: 7, firstName: "Sophie", lastName: "DeBenedetto"},   
    {id: 8, firstName: "Doctor", lastName: "Who"}, 
    {id: 9, firstName: "Amy", lastName: "Pond"}
  ]
} 
```

我们可以看到，`state`包含`attendanceRecords`和`students`，出勤记录是按日期组织的，每个出勤记录对象都包含一个属性`records`，它列出了该日期每个学生的记录。

我们的`ScheduleContainer`组件主要关注从 state 获取出席记录，并将它们传递给 calendar presentational 组件。对于我的日历，我使用了 React DayPicker 库。

```
import React from 'react';
import DayPicker, { DateUtils } from 'react-day-picker'
import {connect} from 'react-redux';
import {bindActionCreators} from 'redux';
import * as attendanceRecordActions from '../../actions/attendanceRecordActions';

class ScheduleContainer extends React.Component {
  componentDidMount() {
    if (this.props.attendanceRecords.length = = 0) {
      this.props.actions.fetchAttendanceRecords();
    }
  }

  render() {
    return (
      < DayPicker
        locale='us'
        selectedDays={day => {
         DateUtils.isSameDay(new Date())
        }} />     )
  }
}

function mapStateToProps(state, ownProps) {   
  return {attendanceRecords: state.attendanceRecords}
}

function mapDispatchToProps(dispatch) {
  return {actions: bindActionCreators(attendanceRecordActions, dispatch)

export default connect(mapStateToProps, mapDispatchToProps)(ScheduleContainer); 
```

到目前为止，我们的组件非常简单。它管理以下内容:

*   使用`mapStateToProps`从 state 获取出席记录，并将其作为道具提供给我们的组件。(这个`state`键的默认值是一个空数组，它是在我们应用程序的初始状态下设置的，这里没有显示。)
*   使用`mapDispatchToProps`来获取`attendanceRecordActions`函数，并使它们对我们在`this.props.actions`下的组件可用。
*   使用生命周期法，`componentDidMount`检查是否存在事实上的考勤记录。如果没有，调度`fetchAttendanceRecords`动作，该动作将进行 API 调用，获取出勤记录，将它们填充到应用程序状态中，并导致重新呈现。
*   然后，呈现`DayPicker`日历组件，通过`selectedDays`属性突出显示选定的日期。

目前，我们还没有对从国务院拿到的出勤记录做任何处理。那么，我们需要对它们做些什么呢？

我们需要:

*   确定所选的日期和学生，并呈现该学生当天的记录。
*   允许用户单击日历日，并更改所选日期和要查看的出勤记录。

#### 第二步:将数据传递给表示组件进行显示

我们的目标是显示选定学生和选定日期的出勤记录。在我们担心如何获得这些信息之前，让我们构建一个简单的功能组件来显示它。

我们将构建一个组件`AttendanceRecordShow`，它将由`ScheduleContainer`呈现。最终，`ScheduleContainer`会将正确的出勤记录(基于选择的学生和日期)传递到这个组件中。

```
// src/components/AttendanceRecordShow.js

import React from 'react'
import Moment from 'react-moment';

const AttendanceRecordShow = (props) => {
  function studentInfo() {
    if (props.student) {
      return (
        < p >
          record for: {props.student.first_name}{props.student.last_name}
        < /p>
    }
  }

  function recordInfo() {
    if (props.record) {
      if (props.record.arrived) {   
        const date = new Date(props.record.arrived_at)   
        return < p>arrived at: {date.toDateString()}< /p>
      } else {
        return < p>absent or late</ p>
      }
    }
  }
  return (
    < div className="col-sm-12 text-center">
      {studentInfo()}
      {recordInfo()}
      < p>{props.day.toDateString()}< /p>
    < /div>
  )
}

export default AttendanceRecordShow 
```

`ScheduleContainer`将组件渲染成这样:

```
// src/components/containers/ScheduleContainer.js

class ScheduleContainer extends React.Component {
  ...
  render() {
    return (
      < DayPicker
        locale='us'
        selectedDays={day => {
         DateUtils.isSameDay(new Date())
        }} />
      < AttendanceRecordShow 
        day={we need to give it a day!} 
        student={we need to give it a student!} 
        record={we need to give it a record!}/>  
    )
} 
```

我们的`ScheduleContainer`容器负责获取和操作数据，并将其传递给子功能组件或表示组件进行显示。

因此，让我们教`ScheduleContainer`如何识别和获取所选学生和日期的出勤记录，并将其传递给适当的表示组件。

`ScheduleContainer`将需要跟踪所选择的学生、日期和出勤记录，所选择的学生和日期将根据用户从我们的学生列表中点击某个日历日或学生而变化。这将依次改变我们想要显示的出席记录。所以，`ScheduleContainer`应该跟踪这个信息，作为它自己内部状态的一部分。

我们将从给`ScheduleContainer`一个设置一些默认值的构造函数开始。我们将给`selectedDay`属性一个今天的默认值，`selectedStudent`属性一个默认值`null`，给`selectedRecord`一个默认值`null`。

```
// src/components/containers/ScheduleContainer.js

class ScheduleContainer extends React.Component {
  constructor(props) {
    super(props)
    this.state = {selectedStudent: null, selectedRecord: null, selectedDay: new Date()}
  }
  ...

  render() {
    return (
      < DayPicker
        locale='us'
        selectedDays={day => {
         DateUtils.isSameDay(new Date())
        }} />       < AttendanceRecordShow 
        day={this.selectedDay} 
        student={this.selectedStudent} 
        record={this.selectedRecord}/>  
    )
} 
```

我们需要给用户改变所选日期的能力，即选择一天。`DayPicker`组件响应一个回调函数`onClick`，我们可以将它设置为一个自定义函数来设置我们选择的日期。这样，当用户点击日历日时，我们可以动态更新`ScheduleContainer`组件的 state 的`selectedDay`属性，改变我们传递给`AttendanceRecordShow`的值。

让我们定义一个函数`selectDay`，并告诉它作为`DayPicker`组件的`onClick`函数来触发。我们的`selectDay`功能有两项工作:

*   将`ScheduleContainer`组件状态的`selectedDay`属性设置为用户通过日历点击的日期。
*   如果已经选择了一个学生，选择一天*应该会将州的`selectedRecord`属性更改为该天所选学生的记录*。

```
selectDay(e, day) {
    e.preventDefault();
    if (this.state.selectedStudent) {
      const recordsBySelectedDate = this.props.attendanceRecords.find(recordsByDate => {
        const date = new Date(recordsByDate.date)
        return date.toDateString() = = day.toDateString()
      })
      const record = recordsBySelectedDate.records.find(record => record.student_id = = this.state.selectedStudent.id)
      this.setState({selectedRecord: record, selectedDay: day}) 
    } else {
      this.setState({selectedDay: day})
    }
  } 
```

在上面的函数中，我们首先检查是否有一个`selectedStudent`，如果有，我们接着获取具有新选择的日期的出勤记录，然后从该组记录中，获取具有所选择的学生 ID 的`student_id`的记录。

接下来，让我们为用户提供从学生列表中选择学生的能力。

#### 第三步:作为回调函数的道具:将动作从表示层发送到容器组件

我们将构建一个表示组件`StudentList`，它将呈现学生列表。用户应该能够点击列表中的任何学生，并查看该学生在所选日期的出勤记录。

但是，我们的`StudentList`将需要访问所有的学生，以便显示他们。`StudentList`不应该获取任何数据本身，或者以任何方式连接到商店——记住，它只是一个愚蠢的表示组件。我们有一个容器组件`ScheduleContainer`，它负责获取数据。但是该容器组件已经在获取出勤记录数据。我们不想让这个容器组件承担太多太多的数据获取责任。

因此，我们将构建另一个容器组件并让`ScheduleContainer`包含它。这说明了我们的容器模式的一个重要方面:

**容器可以包含其他容器！**

因此，我们将构建另一个容器组件`StudentsContainer`，它将获取学生数据并将其传递给表示组件`StudentList`，作为`props`的一部分

#### `StudentsContainer`分量

`StudentsContainer`应该遵循与`ScheduleContainer`类似的模式——使用`mapStateToProps`获取学生，如果没有学生被填充到 state 中，则使用`componentDidMount`生命周期方法从 API 中获取学生。

我们开始吧！

```
import React from 'react';
import {connect} from 'react-redux';
import {bindActionCreators} from 'redux';
import * as instructorActions from '../../actions/instructorActions';
import StudentList from '../studentList';

class StudentsContainer extends React.Component {
  componentDidMount() {
    if (this.props.students.length = = 0) {
      this.props.actions.fetchStudents();
    }

  }

  render() {
    return ( 
      < div className="col-lg-4">
        < h2>Students< /h2>
        < StudentList 
          students={this.props.students}/>
      < /div> 
    )
  }
}

function mapStateToProps(state) {
  return {students: state.students}
}

function mapDispatchToProps(dispatch) {
  return {actions: bindActionCreators(instructorActions, dispatch)}
}

export default connect(mapStateToProps, mapDispatchToProps)(StudentsContainer); 
```

该组件从 state 中挑选学生，并将他们传递给 presentational 组件`StudentList`。

我们的`StudentList`组件看起来像这样:

```
import React from 'react'
import {ListGroup, ListGroupItem} from 'react-bootstrap'

const StudentList = (props) => {
  function studentListItems() {
    return props.students.map((student, i) => {
      return (
        < ListGroupItem>
         {student.first_name} {student.last_name}
        < /ListGroupItem>
    })
  }

  function studentListGroup() {
    return (
      < ListGroup>
        {studentListItems()}
      < /ListGroup>
    )
  }
  return (
    {studentListGroup()}
  )
}

export default StudentList; 
```

`StudentList`遍历从`StudentsContainer`传下来的`students` prop 中存储的学生，收集并呈现学生姓名列表组。

顶层容器组件，`ScheduleContainer`将把`StudentsContainer`渲染成这样:

```
// src/components/containers/ScheduleContainer.js

class ScheduleContainer extends React.Component {
  constructor(props) {
    super(props)
    this.state = {selectedStudent: null, selectedRecord: null, selectedDay: new Date()}
  }
  ...
  render() {
    return (
      <StudentsContainer />
      < DayPicker
        locale='us'
        selectedDays={day => {
         DateUtils.isSameDay(new Date())
        }} />       < AttendanceRecordShow 
        day={this.selectedDay} 
        student={this.selectedStudent} 
        record={this.selectedRecord}/>  
    )
} 
```

现在，我们已经建立了学生列表，并运行和显示了一个可爱的学生列表，我们需要允许我们的用户单击列表中的一个学生，使该学生成为“选定的学生”，并显示该学生在选定日期的出勤记录。

#### 作为回调函数的道具+数据向下动作向上的原理

还记得是谁负责识别考勤记录的吗？它必须知道选定的日期*和*，选定的学生*和*可以访问所有的出勤记录...

是`ScheduleContainer`！因此，因为是`StudentList`负责呈现我们的学生列表，所以我们必须教`StudentList`如何将消息一路发送回顶级容器`ScheduleContainer`，并告诉它每当用户点击一个学生时更新它的`selectedStudent`属性。

我们将在`ScheduleContainer`中定义一个函数`selectStudent`。这个函数将接受一个被选中学生的 ID 参数，并相应地更新`ScheduleContainer`所在州的`selectedStudent`。

它还有第二个责任。它必须根据新选择的学生和当前选择的日期更新组件状态的`selectedRecord`属性。

最后，我们必须通过`StudentsContainer`将这个函数向下传递到`StudentList`作为道具，并且我们需要在顶层容器中的构造函数中使用`bind` `this`来实现这个功能。

```
// src/components/containers/ScheduleContainer.js

class ScheduleContainer extends React.Component {
  constructor(props) {
    super(props)
    this.selectStudent = this.selectStudent.bind(this)
    this.state = {selectedStudent: null, selectedRecord: null, selectedDay: new Date()}
  }
  ...
  selectStudent(studentId) {
    const student = this.props.students.find(student => student.id = = studentId)
    var that = this
    const recordsBySelectedDate = this.props.attendanceRecords.find(recordsByDate => {
      const date = new Date(recordsByDate.date)
        return date.toDateString() == that.state.selectedDay.toDateString()
    })
    const record = recordsBySelectedDate.records.find(record => record.student_id studentId)
    this.setState({selectedStudent: student, selectedRecord: record})
  }
  render() {
    return (
      < StudentsContainer 
        selectStudent={this.selectStudent}/>
      < DayPicker
        locale='us'
        selectedDays={day => {
         DateUtils.isSameDay(new Date())
        }} />       < AttendanceRecordShow 
        day={this.selectedDay} 
        student={this.selectedStudent} 
        record={this.selectedRecord}/>  
    )
} 
```

`StudentsContainer`将依次将`selectStudent`功能向下传递给`StudentList` :

```
import React from 'react';
import {connect} from 'react-redux';
import {bindActionCreators} from 'redux';
import * as instructorActions from '../../actions/instructorActions';
import StudentList from '../studentList';

class StudentsContainer extends React.Component {
  componentDidMount() {
    if (this.props.students.length == 0) {
      this.props.actions.fetchStudents();
    }

  }

  render() {
    return ( 
      <div className="col-lg-4">
        <h2>Students</h2>
        <StudentList 
          students={this.props.students}
          selectStudent={this.props.selectStudent}/>
      </div> 
    )
  }
}

function mapStateToProps(state) {
  return {students: state.students}
}

function mapDispatchToProps(dispatch) {
  return {actions: bindActionCreators(instructorActions, dispatch)}
}

export default connect(mapStateToProps, mapDispatchToProps)(StudentsContainer); 
```

对于每个学生列表项:
，`StudentList`将触发`selectStudent`作为`onClick`功能

```
import React from 'react'
import {ListGroup, ListGroupItem} from 'react-bootstrap'

const StudentList = (props) => {
  function triggerSelectStudent(e) {
    e.preventDefault();
    props.selectStudent(e.target.id)
  }

  function studentListItems() {
    return props.students.map((student, i) => {
      return (
        < ListGroupItem onClick={triggerSelectStudent} id={student.id}>
          {student.first_name} {student.last_name} 
        < /ListGroupItem>
      )
    })
  }

  function studentListGroup() {
    return (
      < ListGroup>
        {studentListItems()}
      < /ListGroup>
    )
  }
  return (
    {studentListGroup()}
  )
}

export default StudentList; 
```

这里，我们定义了一个函数`triggerSelectStudent`，它在点击学生列表项时触发。该函数获取被点击的学生的 ID，并将其传递给对`selectStudent`函数的调用，作为道具传递给该组件。这将沿着组件树一路返回到`ScheduleContainer`，调用在那里定义的`selectStudent`函数。顺便说一下，这是 React 非常擅长的**数据向下动作向上**流的一个很好的例子。

该函数将运行，将`ScheduleContainer`的状态更改为新的`selectedStudent` *和新的*`selectedRecord`，这将触发组件重新渲染。

这将重新呈现`ScheduleContainer`包含的`AttendanceRecordShow`组件，为用户呈现新选择的出勤记录。

### 结论

唷！我们做到了！好吧，太多了。此处提供的代码是为该应用程序构建功能的一种非常具体的方法，但它说明了更大的容器模式，其中:

*   顶层容器呈现组件树的其余部分
*   该容器保存子表示组件，以及其他依次保存表示组件的容器
*   容器负责从状态中获取数据，并更新内部状态以响应用户交互
*   当需要通过 DDAU 模式进行用户触发的更改时，表示组件负责接收来自其父组件的数据，以显示和警告其父组件

和往常一样，有不止一种方法来实现给定的特性，但是这里显示的实现*是符合上述原则的*。要查看我们这个项目的完整代码，可以查看[这个回购](https://github.com/antoinfive/flatiron-attendance-trackr-frontend)。

编码快乐！