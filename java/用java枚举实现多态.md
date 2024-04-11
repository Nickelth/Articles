使用Sqlancer的时候发现了一个比较有趣的用法。

首先看以下几个文件：

test.java:

```java
package test;
import java.util.*;


public class test {
	enum Action implements AbstractAction{
		ACTION1(ActionProvider1::Function1),
		ACTION2(ActionProvider2::Function2);
		QueryProvider provider;
		QueryProvider provider2;
		Action(QueryProvider q) {
			this.provider = q;
		}
		@Override
		public String GetQuery()
		{
			return provider.GetQuery();
			//return "123";
		}
	}
    
    // 测试枚举类型中的方法
    public static void main(String[] args) {

        StatementsExecutor<Action> se = new StatementsExecutor<Action>(Action.values());
        se.executeStatements();
    }


}



```

AbstractAction.java:

```java
package test;

public interface AbstractAction {
	String GetQuery();
}

```

ActionProvider1.java:

```java
package test;

public class ActionProvider1 {
	public static String Function1()
	{
		return "This is function1.";
	}
}

```

ActionProvider2.java:

```java
package test;

public class ActionProvider2 {
	public static String Function2()
	{
		return "This is function2.";
	}
}
```

QueryProvider.java:

```java
package test;

public interface QueryProvider {
	public String GetQuery();
}
```

StatementsExecutor.java:

```java
package test;

public class StatementsExecutor<A extends AbstractAction> {
	A[] actions;
	public StatementsExecutor(A[] actions)
	{
		this.actions = actions.clone();
	}
	public void executeStatements() {
		// TODO Auto-generated constructor stub
		for(int i = 0; i <actions.length; i ++ )
		{
			System.out.println(actions[i].GetQuery());
		}
	}
}
```

运行结果为：

```
This is function1.
This is function2.
```



在上面这些文件组成的例子中，将静态方法（Function1和Function2）作为方法引用传入枚举类型作为初始化参数，得到两个枚举类型实例ACTION1和ACTION2，然后分别执行GetQuery得到结果。这里，Java会自动用方法引用初始化QueryProvider的GetQuery方法。

方法引用也可以换为lambda表达式。