# Переход к пакетным заданиям из диалога
## История задачи
Как известно, пакетные задания создаются из классов наследников RunBaseBatch и SysOperationController. И если в системе несколько сотен активных пакетных заданий и ещё больше завершенных, то найти ранее созданное пакетное задание для отдельно взятого класса становится очень сложно. Предлагается логичный вариант - сделать переход к пакетным заданиям из диалога классов. А также реализовать переход к журналу пакетных заданий, потому что само пакетное задание может быть удалено пользователем или автоматически (в случае с рантайм-пакетами).
## Ax2012
Для реализации задачи в Ax2012 необходимо импортировать файл [Class_BatchJobOpenFromDialog_CDT.xpo](Ax2012\Class_BatchJobOpenFromDialog_CDT.xpo) и внести следующие изменения в стандартные классы и формы :  
### Метод BatchDialog.dialog
```
    buttonGrp = dialog.addGroup("@SYS40",grp2Col);
    buttonGrp.frameType(FormFrameType::None);
    buttonGrp.leftMode(3);

    if (!disableRecurrenceButton)
    {
        dialog.addMenuItemButton(MenuItemType::Display,menuitemDisplayStr(SysRecurrenceRun),DialogMenuItemGroup::CurrentGrp);
    }
    dialog.addMenuItemButton(MenuItemType::Display, menuitemDisplayStr(BatchAlerts), DialogMenuItemGroup::CurrentGrp);
    // --> Ax-OpenBatchJobFromDialog
    BatchJobOpenFromDialog_CDT::addMenuItemButtonsToDialog(dialog);
    // <-- Ax-OpenBatchJobFromDialog

    textGrp = dialog.addGroup();
    textGrp.frameType(FormFrameType::Edged3DLine);
    textGrp.alignChild(false);
    textGrp.widthMode(1);
    this.batchInfo().fieldText(dialog.addText(SysRecurrenceRun::toText(this.batchInfo().parmBatchHeader().parmRecurrenceData())));

```
### Метод BatchInfo.dialog
```
    buttonGrp = dialog.addGroup('',grp2Col);
    buttonGrp.frameType(FormFrameType::None);
    buttonGrp.leftMode(3);

    dialog.addMenuItemButton(MenuItemType::Display,menuitemDisplayStr(SysRecurrenceRun),DialogMenuItemGroup::CurrentGrp);
    dialog.addMenuItemButton(MenuItemType::Display,menuitemDisplayStr(BatchAlerts), DialogMenuItemGroup::CurrentGrp);
    // --> Ax-OpenBatchJobFromDialog
    BatchJobOpenFromDialog_CDT::addMenuItemButtonsToDialog(dialog);
    // <-- Ax-OpenBatchJobFromDialog

    dialog.curFormGroup(dialog.mainFormGroup());
    textGrp = dialog.addGroup();
    textGrp.frameType(FormFrameType::Edged3DLine);
    textGrp.alignChild(false);
    textGrp.widthMode(1);
    fieldText = dialog.addText(SysRecurrenceRun::toText(this.parmBatchHeader().parmRecurrenceData()));
```
### Форма BatchJob
В источнике данных BatchJob в методе init в конце добавить :  
```
    BatchJobOpenFromDialog_CDT::applyFilter2BatchJob(this);
```
### Форма BatchJobHistory
В источнике данных BatchJobHistory в методе init в конце добавить :  
```
    BatchJobOpenFromDialog_CDT::applyFilter2BatchJobHistory(this);
```
