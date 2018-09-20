## JDK UUID　
[部分引用csdn gaochen519](https://blog.csdn.net/gaochen519/article/details/52472162/)
> UUID通用唯一识别码 (Universally Unique Identifier),这是一个软件建构的标准

> UUID 的目的，是让分布式系统中的所有元素，都能有唯一的辨识资讯，而不需要透过中央控制端来做辨识资讯的指定。如此一来，每个人都可以建立不与其它人冲突的 UUID。在这样的情况下，就不需考虑数据库建立时的名称重复问题。目前最广泛应用的 UUID，即是微软的 Microsoft's Globally Unique Identifiers (GUIDs)，而其他重要的应用，则有 Linux ext2/ext3 档案系统、GNOME、KDE、Mac OS X 等等。

- 相关数据库操作 生成主键值

> 在JAVA开发中，对数据库进行数据操作时，尤其是插入数据时，在很多场景中会遇到返回主键这个问题。而一般情况下，我们会设计数据表的主键为自增，对于支持返回主键的数据库来说，我们想要获取返回的主键只需要设置一下属性即可，但是对于那些不支持返回主键的数据库，这是一个很头疼的问题

- Java 自带的UUID 重复问题

> 大多数人多是uuid 重复的概率很小基本，但是uuid在我这里还是重复了，在甲方公司做开发，数据库表数据大概是200多万条。

> 有定时任务，每天都会执行定时任务，进行数据插入，每天基本上也是5.6万条吧， 用到的主键id 就是用的uuid , ==连续隔了一天出现了两次主键重复冲突异常==，

> 查找了一下原因， 因为客户这边的机子都是集群的，有两台机子再跑定时任务，逻辑上会加上redis锁，这样只能一台机器跑业务，但是问题就出在这里，

> 下次执行定时任务的时候不知道是哪台机器，反正我现在是没有搞清楚原因,我解决办法就是==直接加上时间戳拼成主键ID==

```
public class IDGenerator {
	/**
	 * 生成主键(32位)
	 * @return
	 */
	public static String generateID(){
		return generateID(System.currentTimeMillis());
	}
	/**
	 * 根据指定时间生成主键
	 * @param time
	 * @return
	 */
	public static String generateID(long time){
		String rtnVal = Long.toHexString(time);
		rtnVal += UUID.randomUUID();
		return rtnVal.substring(0, 32);
	}
}
```
