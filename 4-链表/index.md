# 4.链表

链表container/list

* 实体：`List`（双向链表）、`Element`(链表中元素的结构)

* 方法们

  ```
  func (l *List) MoveBefore(e, mark *Element)
  func (l *List) MoveAfter(e, mark *Element)
  
  func (l *List) MoveToFront(e *Element)
  func (l *List) MoveToBack(e *Element)
  
  func (l *List) Front() *Element
  func (l *List) Back() *Element
  
  func (l *List) InsertBefore(v interface{}, mark *Element) *Element
  func (l *List) InsertAfter(v interface{}, mark *Element) *Element
  
  func (l *List) PushFront(v interface{}) *Element
  func (l *List) PushBack(v interface{}) *Element
  ```

* 为什么链表可以做到开箱即用？
  * 延迟初始化；同样的，切片也起到了延迟初始化底层数组的作用
    * 优点：把计算压力从变量声明时延迟到真正使用变量时
    * 缺点：每次使用变量之前都需要判断该变量是否已经被初始化了，拖慢性能
  * 但是链表的`PushFront`方法、`PushBack`方法、`PushBackList`方法以及`PushFrontList`方法，本来就需要先判断链表的状态，并且一个新链表使用之前一定会调用这些方法的其中一个，巧妙的平衡了延迟初始化的缺点。




