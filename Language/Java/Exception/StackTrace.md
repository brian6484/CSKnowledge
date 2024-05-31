## StackTrace
So coming back from [NPE](https://github.com/brian6484/CSKnowledge/blob/main/Language/Java/Exception/NullPointerException.md)
we should know how to analyse stack trace, and if there are potential problems in 1 single line, we should kinda cancel out
the obvious ones.

## Example
[very good ref](https://okky.kr/articles/338405)

Lets look at this mega long stack trace
```
java.lang.reflect.InvocationTargetException
     at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
     at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
     at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
     at java.lang.reflect.Method.invoke(Unknown Source)
     at com.mylibrary.ap.xul.builder.handler.MethodCallback.invokeCallback(MethodCallback.java:32)
     at com.mylibrary.ap.xul.builder.handler.ViewHandler.invokeActionResult(ViewHandler.java:581)
     at com.mylibrary.ap.xul.context.impl.ViewContextImpl$2.onActionResult(ViewContextImpl.java:283)
     at com.mylibrary.ap.xul.context.impl.ActionContextImpl.setResult(ActionContextImpl.java:90)
     at com.mylibrary.ap.xul.action.stock.DialogAction.handleDialogClose(DialogAction.java:156)
     at com.mylibrary.ap.xul.action.stock.DialogAction$1.windowClosed(DialogAction.java:142)
     at com.mylibrary.api.Window.fireWindowClosedEvent(Unknown Source)
     at com.mylibrary.api.Window.onClose(Unknown Source)
     at com.mylibrary.api.platform.WindowBase.BaseClose(Native Method)
     at com.mylibrary.api.platform.WindowBase.BaseClose(Unknown Source)
     at com.mylibrary.api.Window.close(Unknown Source)
     at com.mylibrary.ap.xul.ui.MessageDialog.handleButtonClick(MessageDialog.java:145)
     at com.mylibrary.ap.xul.ui.MessageDialog$1.handleClick(MessageDialog.java:134)
     at com.mylibrary.api.ClickableSupport.fireClickEvent(Unknown Source)
     at com.mylibrary.api.ClickableSupport.handleAction(Unknown Source)
     at com.mylibrary.api.Button.actionHook(Unknown Source)
     at com.mylibrary.api.Component.action(Unknown Source)
Caused by: java.lang.NullPointerException
     at com.mycompany.service.impl.PortalManagerImpl.deleteMenuItem(PortalManagerImpl.java:603)
     at com.mycompany.service.impl.PortalManagerImpl.deletePortal(PortalManagerImpl.java:358)
     at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
     at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
     at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
     at java.lang.reflect.Method.invoke(Unknown Source)
     at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:307)
     at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:182)
     at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:149)
     at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:106)
     at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:171)
     at org.springframework.security.intercept.method.aopalliance.MethodSecurityInterceptor.invoke(MethodSecurityInterceptor.java:66)
     at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:171)
     at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:204)
     at $Proxy54.deletePortal(Unknown Source)
     at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
     at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
     at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
     at java.lang.reflect.Method.invoke(Unknown Source)
     at com.mycompany.util.SpringSecurityContextInvocationHandler.invoke(SpringSecurityContextInvocationHandler.java:62)
     at $Proxy84.deletePortal(Unknown Source)
     at com.mycompany.ui.binding.PortalDataProvider.doDelete(PortalDataProvider.java:81)
     at com.mycompany.ui.binding.PortalDataProvider.doDelete(PortalDataProvider.java:12)
     at com.mycompany.ui.binding.AbstractEISDataProvider.delete(AbstractEISDataProvider.java:105)
     at com.mylibrary.ap.xul.binding.dataset.impl.DatasetImpl.doCommit(DatasetImpl.java:90)
     at com.mylibrary.ap.xul.binding.dataset.impl.AbstractDataset.commit(AbstractDataset.java:251)
     at com.mylibrary.ap.xul.binding.dataset.impl.AbstractDataset.deleteRow(AbstractDataset.java:201)
     at com.mylibrary.ap.xul.action.dataset.DeleteDataRowAction.execute(DeleteDataRowAction.java:22)
     at com.mylibrary.ap.xul.context.impl.ViewContextImpl.execute(ViewContextImpl.java:294)
     at com.mycompany.ui.portal.PortalInfoHandler.onPostConfirmDeleteAction(PortalInfoHandler.java:192)
     ... 21 more
```

stack trace tells us about the types of exceptions (if code implemented catching those exceptions) and where they are
caused via "caused by".

We can see that NPE is caused here
```
Caused by: java.lang.NullPointerException
     at com.mycompany.service.impl.PortalManagerImpl.deleteMenuItem(PortalManagerImpl.java:603)
     at com.mycompany.service.impl.PortalManagerImpl.deletePortal(PortalManagerImpl.java:358)
```

So lets look at the trace. While executing the deletePortal method at line 358 in PortalManagerImpl package, 
that method **invoked** deleteMenuItem method. Then, within that deleteMenuItem method at line 603 in the same package, NPE is caused. So lets look at the code

```java
if (item == null) {
    throw new NullArgumentException("item");
}

//중간 생략
List<PortalMenu> children = getMenuItems(item.getPortal().getId(), item.getId()); // 603번째 줄

for (PortalMenu child : children) {
    deleteMenuItem(child);
}
```

So we revised how null pointers are thrown and not thrown. We can see 5 possible cases
1) children
2) item
3) item.getId()
4) item.getPortal()
5) item.getPortal().getId()

So lets see which case caused this

## Identifying causes
1) children - NPE can't be raised just because you assign a null value is assigned to this children variable. Only when you try using its method or a field attribute will it cause NPE

2) item - we see that if item is null, that is checked in the previous line to throw NullArgumentException. So it
is no prob

3) item.getId() - remember you can **pass null to method argument** just fine.
4) item.getPortal() - if item.getPortal() is null, and we do a .getId() method call on it, it **will cause NPE**
5) item.getPortal().getId() - same here, even if that is null, you can pass null value to method argument like point 3 so it isnt a problem

Really useful to use stack trace

