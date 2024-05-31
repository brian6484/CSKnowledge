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
