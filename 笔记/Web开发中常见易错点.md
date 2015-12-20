**长期更新！**  

**(JQuery)**
1.JQuery中的ajax函数，当返回对象是一个XML的时候，我们通过指定dataType指定返回值的类型。但是记得XML一定得大写，如果写成小写，会导致获取不到服务端返回的数据
$.ajax({
	dataType:'XML'
})

**(JQuery)**
2.接着上面一个问题，如果服务端返回的是xml，我们读取的时候需要将返回的data转换成jquery对象，然后再获取属性值。
$(data).text();或者$(data)[0].innerHTML;

**(Hibernate)**
3.使用composite-id映射联合主键的JavaBean对象必须实现序列化接口
JavaBean.hbm.xml映射文件的源代码如下：

	<composite-id>
		<!-- 映射组件主键里的各属性 -->
		<key-property name="mid" type="string" column="mid"></key-property>
		<key-property name="pid" type="string" column="pid"></key-property>
	</composite-id>

JavaBean的代码如下：注意该Bean实现了Serializable

	public class ElecPopedom implements Serializable {
		private String mid;
		private String pid;
	}	

**(Hibernate)**
4.JavaBean中一旦有is开头的属性，一定要注意

	private boolean isParent;
	private boolean isMenu;

使用Eclipse为我们创建的代码是：

	public boolean IsParent() {
	//is开头不符合规则，必须修改成get，否则Hibernate识别不了
		return isParent;
	}
	public void setParent(boolean isParent) {
		this.isParent = isParent;
	}
	public boolean IsMenu() {
		return isMenu;
	}
	public void setMenu(boolean isMenu) {
		this.isMenu = isMenu;
	}


必须改成下面这样的代码


	public boolean getIsParent() {
		return isParent;
	}
	public void setIsParent(boolean isParent) {
		this.isParent = isParent;
	}
	public boolean getIsMenu() {
		return isMenu;
	}
	public void setIsMenu(boolean isMenu) {
		this.isMenu = isMenu;
	}