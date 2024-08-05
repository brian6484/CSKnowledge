## Issue
When you are trying to project to a dto object with the results of your querydsl query, it is easy. But when we need some
conditional statements, we can use CaseBuilder. For example, if I have 2 fields and I want to assign only one of those 2 fields
as "deviceInfo" if it is not null and not whitespace, we need a CaseBuilder to enforce some conditions.

## Solution
```java
public List<DeviceIssueInfo> getDeviceInfoList() {
    QYourEntity yourEntity = QYourEntity.yourEntity;

    // Define the condition to select macAddress if it's not empty, otherwise select serialNo
    StringExpression deviceInfo = new CaseBuilder()
        .when(yourEntity.macAddress.isNotNull().and(yourEntity.macAddress.ne("")))
        .then(yourEntity.macAddress)
        .when(yourEntity.serialNo.isNotNull().and(yourEntity.serialNo.ne("")))
        .then(yourEntity.serialNo)
        .otherwise("");

    List<DeviceIssueInfo> deviceInfoList = queryFactory
        .select(Projections.fields(DeviceIssueInfo.class,
            deviceInfo.as("deviceInfo")
        ))
        .from(yourEntity)
        .fetch();

    return deviceInfoList;
}
```
