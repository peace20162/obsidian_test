Table of Content
=====================
- [[#Overview|Overview]]
- [[#Related Files|Related Files]]
	- [[#Related Files#1. MailService.java|1. MailService.java]]
	- [[#Related Files#2. MainModels.java|2. MainModels.java]]
	- [[#Related Files#3. rp003.jsp|3. rp003.jsp]]
	- [[#Related Files#4. rp003mail.jsp|4. rp003mail.jsp]]
	- [[#Related Files#5. RP003MailDisplay.java|5. RP003MailDisplay.java]]
- [[#Related Database Table|Related Database Table]]
	- [[#Related Database Table#- SQL Server|- SQL Server]]
	- [[#Related Database Table#- Oracle DB|- Oracle DB]]
- [[#Code Section|Code Section]]
	- [[#Code Section#Python 🐍|Python 🐍]]

## Overview

นี้คือเอกสารสำหรับระบบการส่งเมลล์เพื่อแจ้งเตือนผู้ยืมเงินยืมทดรองโดยแบ่งเมลล์เป็น 2 ประเภท

1. เมลล์สำหรับคนที่ได้รับเงินมา 13 วัน
2. เมลล์สำหรับคนที่ได้รับเงินมาเกินกำหนด (15 วัน)

## Related Files

นี้คือ list files ที่มีความเกี่ยวข้องกับระบบนี้

### 1. MailService.java

    ไฟล์นี้เป็นไฟล์สำหรับการส่งอีเมลล์ซึ่งจะเป็นไฟล์หลักในการทำหน้าที่ส่งเมลล์ทุกวันผ่าน Window Scheduler ในเครื่อง Host (172.20.1.106)

### 2. MainModels.java
    
### 3. rp003.jsp
    
### 4. rp003mail.jsp
    
### 5. RP003MailDisplay.java
    

## Related Database Table

### - SQL Server
    
    1. per_pertab
        
        สำหรับดึงข้อมูลบุคคล eg. depcode, posbcode
        
    2. per_email
        
        สำหรับดึงอีเมลล์
        
### - Oracle DB
    
    1. PTY_DAT_WTD
        
    2. PTY_DAT_WTD_DTL
        
    3. PTY_DAT_CON
        
    4. PTY_MAIL
        
        สำหรับ้เก็บ log และ แสดงผลบนหน้าเว็บ /RP003MailDisplay


## Code Section

### Python 🐍

```python {label='block 1'}
print('running block 1')
```

```python {label='block 2'}
print('running block 2')
```

```run-python {import=['block 1', 'block 2']}
print('should run block 1 and 2')
```


```run-python
import seaborn as sns
import matplotlib.pyplot as plt
sns.set_style("darkgrid")
iris = sns.load_dataset('iris')
sns.FacetGrid(iris, hue ="species", height = 5)
		.map(plt.scatter, 'sepal_length', 'petal_length')
		.add_legend()
plt.show()
```

