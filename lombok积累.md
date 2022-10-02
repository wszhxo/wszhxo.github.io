# lombok积累

### 以下在实体类添加

@Data  可以不用写getter setter


@Accessors(chain = true)   建造者模式，链式设置属性返回this

@Builder  建造者模式链式设置属性返回this    举例：实体类.builder().设置属性.build();

@ToString  可以不用写 toString 方法



@
