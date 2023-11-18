
# Table of content
===============================================================
- [[#🛠️ sendMail(`WTDModel wm,String empMail, String type, String userType`)|🛠️ sendMail(`WTDModel wm,String empMail, String type, String userType`)]]
	- [[#🛠️ sendMail(`WTDModel wm,String empMail, String type, String userType`)#Code Sample|Code Sample]]
	- [[#🛠️ sendMail(`WTDModel wm,String empMail, String type, String userType`)#🔹เมลล์สำหรับ หลัง Deadline 15 วัน|🔹เมลล์สำหรับ หลัง Deadline 15 วัน]]
	- [[#🛠️ sendMail(`WTDModel wm,String empMail, String type, String userType`)#🔹เมลล์สำหรับ ก่อนถึง Deadline ( ถือเงินครบ 13 วัน)|🔹เมลล์สำหรับ ก่อนถึง Deadline ( ถือเงินครบ 13 วัน)]]
	- [[#🛠️ sendMail(`WTDModel wm,String empMail, String type, String userType`)#Parameters|Parameters]]
- [[#🛠️ getEmpMail(`String empId`)|🛠️ getEmpMail(`String empId`)]]
	- [[#🛠️ getEmpMail(`String empId`)#Code Sample|Code Sample]]
	- [[#🛠️ getEmpMail(`String empId`)#Parameters|Parameters]]
- [[#🛠️ getSuperiorEmpMail(`WTDModel wm`)|🛠️ getSuperiorEmpMail(`WTDModel wm`)]]
	- [[#🛠️ getSuperiorEmpMail(`WTDModel wm`)#Code Sample|Code Sample]]
	- [[#🛠️ getSuperiorEmpMail(`WTDModel wm`)#Parameters|Parameters]]
- [[#🛠️ insertMailLog(`WTDModel wm, int statusEmpMailSend , int statusSupEmpMailSend , String type`)|🛠️ insertMailLog(`WTDModel wm, int statusEmpMailSend , int statusSupEmpMailSend , String type`)]]
	- [[#🛠️ insertMailLog(`WTDModel wm, int statusEmpMailSend , int statusSupEmpMailSend , String type`)#Code Sample|Code Sample]]
	- [[#🛠️ insertMailLog(`WTDModel wm, int statusEmpMailSend , int statusSupEmpMailSend , String type`)#Parameters|Parameters]]
- [[#🛠️ getAutoIncrement(`String SEQ`)|🛠️ getAutoIncrement(`String SEQ`)]]
	- [[#🛠️ getAutoIncrement(`String SEQ`)#Code Sample|Code Sample]]
- [[#🛠️ getDataMoreThan15days()|🛠️ getDataMoreThan15days()]]
- [[#🛠️ getData13Days()|🛠️ getData13Days()]]
	- [[#🛠️ getData13Days()#Code Sample|Code Sample]]
	- [[#🛠️ getData13Days()#Parameters|Parameters]]
- [[#🛠️ main()|🛠️ main()]]
	- [[#🛠️ main()#🔹เมลล์สำหรับคนที่ได้รับเงินมา 13 วัน|🔹เมลล์สำหรับคนที่ได้รับเงินมา 13 วัน]]
	- [[#🛠️ main()#Code Sample|Code Sample]]
	- [[#🛠️ main()#Parameters|Parameters]]
	- [[#🛠️ main()#🔹เมลล์สำหรับคนที่ได้รับเงินมาเกินกำหนด (15 วัน)|🔹เมลล์สำหรับคนที่ได้รับเงินมาเกินกำหนด (15 วัน)]]
	- [[#🛠️ main()#Code Sample|Code Sample]]
	- [[#🛠️ main()#Parameters|Parameters]]
# List of functions

## 🛠️ sendMail(`WTDModel wm,String empMail, String type, String userType`)

เป็นฟังก์ชันสำหรับการส่งเมลล์

### Code Sample

Config.gs_EmailServer อยู่ในไฟล์ Config.java

```java
public static int sendMail(WTDModel wm,String empMail, String type, String userType) {
        int status = 1;
        if(empMail.equals("")){
            return status;
        }
        try{    
            Properties properties = System.getProperties();  
            properties.setProperty("mail.smtp.host", Config.gs_EmailServer);              
            properties.put("mail.smtp.port", portnum);           
            properties.put("mail.smtp.starttls.enable", "true");
            properties.put("mail.smtp.ssl.protocols", "TLSv1.2");

            Session session = Session.getDefaultInstance(properties);  
            
            // Create a default MimeMessage object.
            Message message = new MimeMessage(session);

            // Set From: header field of the header.
            message.setFrom(new InternetAddress(Config.gs_EmailFrom));
            
            // Set To: header field of the header.
            message.setRecipients(Message.RecipientType.TO, InternetAddress.parse(empMail));

            // Set Subject: header field
            String subject = "";
            if(type.equals("AFTER")){
                subject = "ติดตามการถือเงินยืมทดรองเกินกำหนดของบเบิกเงินยืมทดรองที่ "+wm.getWtdNo();
            }
            else{
                subject = "ติดตามครบ 13วัน ของการถือเงินยืมทดรองของบเบิกเงินยืมทดรองที่ "+wm.getWtdNo();
            }
            
            message.setSubject(subject);
                
            // Create the message part
            BodyPart messageBodyPart = new MimeBodyPart();
            Multipart multipart = new MimeMultipart();
            SimpleDateFormat format = new SimpleDateFormat("dd/MM/yyyy");
            DateTimeFormatter fmt = DateTimeFormat.forPattern("dd/MM/yyyy");
            DateTime deadLine = new DateTime(format.parse(wm.getWtdPayDate())).plusDays(15);
            DateTime recieveDate = new DateTime(format.parse(wm.getWtdPayDate()));
            DateTime documentDate = new DateTime(format.parse(wm.getWtdCreateDate()));
             
            System.out.println("Deadline :"+fmt.print(deadLine) );
            System.out.println("Recieve date :"+ fmt.print(recieveDate) );
            System.out.println("Document date:"+fmt.print(documentDate) );
            System.out.println("Amount :"+wm.getWtdDtlAmount() );
            String text;
```

### 🔹เมลล์สำหรับ หลัง Deadline 15 วัน

```java
            if(type.equals("AFTER")){
                if(userType.equals("EMP")){
                    text = "เรียนคุณ  "+wm.getFname()+" "+wm.getSname()+"<br><br>" +
                    "&emsp;&emsp;&emsp;ตามใบเบิกเงินยืมทดรองที่ "+wm.getWtdNo()+" ลงวันที่ "+fmt.print(documentDate)+" ชื่อ "+ wm.getFname()+" "+wm.getSname()+" เบิกเงินยืมทดรองไปเป็นจำนวน " +
                     String.format("%.2f", wm.getWtdDtlAmount())+"  บาท<br>" +
                    "โดยได้รับเงินยืมทดรองจำนวนดังกล่าวไปเมื่อวันที่ "+fmt.print(recieveDate)+"และครบกำหนดหักล้างเงินยืมทดรองวันที่ "+fmt.print(deadLine)
                    +" ซึ่งขณะนี้ท่านได้ถือเงินยืมทดรองเกินกำหนดตามข้อบังคับ กทพ.  ว่าด้วย เงินยืมทดรอง (ฉบับที่ 2) พ.ศ.๒๕๖๕<br>" +
                    "จึงขอให้ท่านรีบดำเนินการส่งใช้เงินยืมทดรองจำนวนดังกล่าวโดยด่วนด้วย<br>"
                    +"<br>"+"<p style='text-align: right;'> สุชิรา  ทองหญีต<br>ตจ.1 กกง.ฝกง.<br>โทร 23161</p><br>";}
                else{
                    text = "เรียนคุณ  "+wm.getSupFname()+" "+wm.getSupSname()+"<br><br>" +
                    "&emsp;&emsp;&emsp;ตามใบเบิกเงินยืมทดรองที่ "+wm.getWtdNo()+" ลงวันที่ "+fmt.print(documentDate)+" ชื่อ "+ wm.getFname()+" "+wm.getSname()+" เบิกเงินยืมทดรองไปเป็นจำนวน " +
                     String.format("%.2f", wm.getWtdDtlAmount())+"  บาท<br>" +
                    "โดยได้รับเงินยืมทดรองจำนวนดังกล่าวไปเมื่อวันที่ "+fmt.print(recieveDate)+"และครบกำหนดหักล้างเงินยืมทดรองวันที่ "+fmt.print(deadLine)
                    +" ซึ่งขณะนี้ท่านได้ถือเงินยืมทดรองเกินกำหนดตามข้อบังคับ กทพ.  ว่าด้วย เงินยืมทดรอง (ฉบับที่ 2) พ.ศ.๒๕๖๕<br>" +
                    "จึงขอให้ท่านรีบดำเนินการส่งใช้เงินยืมทดรองจำนวนดังกล่าวโดยด่วนด้วย<br>"
                    +"<br>"+"<p style='text-align: right;'> สุชิรา  ทองหญีต<br>ตจ.1 กกง.ฝกง.<br>โทร 23161</p><br>";
                
                }
            }
           
```

### 🔹เมลล์สำหรับ ก่อนถึง Deadline ( ถือเงินครบ 13 วัน)

```java
 else{
                if(userType.equals("EMP")){
                    text="เรียนคุณ "+ wm.getFname()+" "+wm.getSname()+" <br><br>" +
                    "&emsp;&emsp;&emsp;ตามใบเบิกเงินยืมทดรองที่ "+wm.getWtdNo()+" ลงวันที่ "+fmt.print(documentDate)+" ชื่อ "+ wm.getFname()+" "+wm.getSname()+"เบิกเงินยืมทดรองไปเป็นจำนวน " +
                    String.format("%.2f", wm.getWtdDtlAmount())+" บาท<br>" +
                    "โดยได้รับเงินยืมทดรองจำนวนดังกล่าวไปเมื่อวันที่ "+fmt.print(recieveDate)
                    +"ซึ่งจะครบกำหนดหักล้างเงินยืมทดรองวันที่ "+fmt.print(deadLine)+" จึงขอให้ท่านดำเนินการส่งใช้เงินยืมทดรองจำนวนดังกล่าวภายในกำหนดด้วย<br>"+
                    "<br>"+"<p style='text-align: right;'> สุชิรา  ทองหญีต<br>ตจ.1 กกง.ฝกง.<br>โทร 23161</p><br>";}
                
                else{                    
                    text="เรียนคุณ "+ wm.getSupFname()+" "+wm.getSupSname()+" <br><br>" +
                    "&emsp;&emsp;&emsp;ตามใบเบิกเงินยืมทดรองที่ "+wm.getWtdNo()+" ลงวันที่ "+fmt.print(documentDate)+" ชื่อ "+ wm.getFname()+" "+wm.getSname()+"เบิกเงินยืมทดรองไปเป็นจำนวน " +
                    String.format("%.2f", wm.getWtdDtlAmount())+" บาท<br>" +
                    "โดยได้รับเงินยืมทดรองจำนวนดังกล่าวไปเมื่อวันที่ "+fmt.print(recieveDate)
                    +"ซึ่งจะครบกำหนดหักล้างเงินยืมทดรองวันที่ "+fmt.print(deadLine)+" จึงขอให้ท่านดำเนินการส่งใช้เงินยืมทดรองจำนวนดังกล่าวภายในกำหนดด้วย<br>"+
                    "<br>"+"<p style='text-align: right;'> สุชิรา  ทองหญีต<br>ตจ.1 กกง.ฝกง.<br>โทร 23161</p><br>";                            
                }
            }
            
            messageBodyPart.setContent(text, "text/html; charset=utf-8" );
            multipart.addBodyPart(messageBodyPart);
            message.setContent(multipart);
            Transport.send(message);
            status = 2;            
        
        }catch(Exception e){

            System.out.println("Error send mail: " + empMail);
            System.err.println("Error: " + e);

        }
        return status;
    }
```

### Parameters

|Property|Type|Description|
|---|---|---|
|`status`|`int`|Set default as `1` and Change to `2` when all process finished. Return value.|

## 🛠️ getEmpMail(`String empId`)

เป็นฟังก์ชันสำหรับการดึง mail มาจาก empId ใน data ที่เก็บในรูป Object `WTDModel`

### Code Sample

```java
public static String getEmpMail(String empId){
        
        String mail = "";
        if(empId.length()== 10){
            empId = empId.substring(1, 10);
        }
        System.out.println(empId);
        try {
            SQLServerConnector i = new SQLServerConnector();                  
            i.executeQuery(sql);
            while(i.getResult().next()){
                mail = i.getResult().getString("address");
            }
            System.out.println("mail :"+ mail);
            i.close();
        } catch (Exception e) {

            System.out.println("Error send mail to employee : " + empId);
            System.out.println("Error :" + e);

        }
        return mail;
    }
```

ส่วนของการดึงข้อมูล SQL

```sql
    String sql = "SELECT address FROM per_email" +
                    " INNER JOIN per_pertab ON per_pertab.empid = per_email.empid " +
                    " WHERE " +
                    // get EXAT mail ONLY
                    " per_email.seqno = 0 " +
                    " AND per_email.empid = '" +empId+ "' ";
```

### Parameters

|Property|Type|Description|
|---|---|---|
|`mail`|`String`|Return value|

## 🛠️ getSuperiorEmpMail(`WTDModel wm`)

เป็นฟังก์ชันสำหรับการดึง mail ของผู้ที่อยู่เหนือกว่า 1 ขั้นจาก `WTDModel wm` ซ่งในระหว่างทางจะเก็บค่าที่จะนำไปใช้ต่ออย่าง `supFname` และ `supSname` ใน`WTDModel wm`

### Code Sample

```java
public static String getSuperiorEmpMail(WTDModel wm){
        String mail = "";
        String supFname = "";
        String supSname = "";
        String empId = wm.getEmpId();
        String depCode = wm.getDepCode();
        if(empId.length()== 10){
            empId = empId.substring(1, 10);
        }
        System.out.println(empId);
        try {
            SQLServerConnector i = new SQLServerConnector();
          
            i.executeQuery(sql);
            while(i.getResult().next()){
                mail = i.getResult().getString("address");
                supFname = i.getResult().getString("name");
                supSname = i.getResult().getString("famname");
                wm.setSupFname(supFname);
                wm.setSupSname(supSname);
            }
            System.out.println("mail :"+ mail);
            i.close();
        } catch (Exception e) {
            PerPerTab depObj;
            try {
                depObj = PerPerTab.getDivcod(depCode);
                System.out.println("Error send mail to superior in dept" + depObj.getDivabb()+" "+ depCode);
                System.out.println("Error " + e);

            } catch (Exception ex) {

                System.out.println( "Can't get depObj by depCode"+ depCode +"Error " + ex);

            }
            
        }
        return mail;
    }
```

ส่วนของการดึงข้อมูล SQL

```java
  // if have depcod and posbcod
            String sql = "SELECT TOP 1 address,name,famname FROM [dbo].[per_email] "
            + "INNER JOIN per_pertab ON per_pertab.empid = per_email.empid " 
            +"WHERE  per_email.seqno = 0 "
            +"AND depcod = CASE " 
            +"WHEN (SELECT posbcode FROM per_pertab WHERE empid = '"+ empId +"') IS NOT NULL " 
            +"AND SUBSTRING((SELECT depcod FROM per_pertab WHERE empid ='"+ empId +"'),3,6) = '0000' "
                    + " THEN  'G00000' "
            +"WHEN (SELECT posbcode FROM per_pertab WHERE empid = '"+ empId +"') IS NOT NULL " 
            +"AND SUBSTRING((SELECT depcod FROM per_pertab WHERE empid ='"+ empId +"'),5,6) = '00' "
                    + "THEN  STUFF((SELECT depcod FROM per_pertab WHERE empid ='"+ empId +"'),3,4,'0000') "
            +"WHEN (SELECT posbcode FROM per_pertab WHERE empid = '"+ empId +"') IS NOT NULL " 
            +"AND SUBSTRING((SELECT depcod FROM per_pertab WHERE empid ='"+ empId +"'),5,6) <> '00' "
                    + "THEN STUFF((SELECT depcod FROM per_pertab WHERE empid ='"+ empId +"'),5,2,'00') "		
            +"ELSE (SELECT depcod FROM per_pertab WHERE empid ='"+ empId +"') END "
            +"AND posbcode IS NOT NULL "
            +"AND posbcode <> ' ' "
            +"ORDER BY per_email.modifydate DESC " ;
```

### Parameters

|Property|Type|Description|
|---|---|---|
|`mail`|`String`|Return value|
|`supFname`|`String`|Set value to `WTDModel wm`|
|`supSname`|`String`|Set value to `WTDModel wm`|

## 🛠️ insertMailLog(`WTDModel wm, int statusEmpMailSend , int statusSupEmpMailSend , String type`)

ส่วนของการใส่ค่า Log ที่จะนำไปใช้บน ประวัติการส่งเมลล์ ใน RP003

### Code Sample

```java
public static int insertMailLog(WTDModel wm, int statusEmpMailSend , int statusSupEmpMailSend , String type){
        
        int status = 0; 
        
        try {
            
            OracleConnector con = new OracleConnector();
            int jobId = getAutoIncrement("SEQ_PTY_MAIL");
            String currentDate =formatNOW("yyyyMMdd", false);
            String sql = "INSERT INTO PTY_MAIL(JOB_ID,WTD_ID,EMP_MAIL,SUP_MAIL,STATUS_EMP_MAILSEND,STATUS_SUPERIOREMP_MAILSEND ,MAIL_SENT_DATE,TYPE)" +
                        "VALUES ("+jobId+","+Integer.parseInt(wm.getWtdId())+",'"+wm.getEmpMail()+"','"+wm.getSupMail()+"','"+statusEmpMailSend+"','"
                    +statusSupEmpMailSend+"','"+currentDate+"','"+type+"')"; 
            status = con.executeUpdate(sql);
            System.out.println("Success insert to logdb wtd_Id: " + wm.getWtdId());
            con.close();
          
        } catch(Exception e){
            System.out.println("Failed insert to logdb wtd_Id: " + wm.getWtdId());
            System.out.println("Error :" + e);

        } 
        
        return status;
    }
```

### Parameters

|Property|Type|Description|
|---|---|---|
|`status`|`int`|Return value|

## 🛠️ getAutoIncrement(`String SEQ`)

เพิ่มเลขให้ `JOB_ID` ใน table คล้าย auto increment ใน postgresSQL

### Code Sample

```java
public static int getAutoIncrement(String SEQ) throws Exception {
        OracleConnector i = new OracleConnector();
        String Sequence = SEQ + ".nextval";
        String sql = "SELECT "
                + Sequence
                + " FROM dual ";
        i.executeQuery(sql);
        i.getResult().next();
        int id = (int) i.getResult().getLong(1);
        i.close();
        return id;
    }
```

## 🛠️ getDataMoreThan15days()

code แบบเดี่ยวกับ getData13Days() แต่แก้เป็น

```java
if (REPORTUtil.getHoding2Count(FormateDate.convertNVarcharDateOracleToWeb
                (con.getResult().getString("WTD_PAY_DATE")),currentDate) >= 15)
```

## 🛠️ getData13Days()

ในส่วนนี้ทำการส่งข้อมูลที่ของใบกำกับที่มีระยะเวลา wtd_pay_date ถึง วันนี้ เป็น 13

### Code Sample

```java
public static List<WTDModel> getData13days(){
        List<WTDModel> l = new ArrayList<WTDModel>();
        String currentDate = FormateDate.now();       
        try {
            OracleConnector con = new OracleConnector();           
           
            con.executeQuery(sql);
            while (con.getResult().next()) {
                        WTDModel wm = new WTDModel();
                        // After paid date exactly 13 days, get all data
                        if (REPORTUtil.getHoding2Count(FormateDate.convertNVarcharDateOracleToWeb
			                (con.getResult().getString("WTD_PAY_DATE")),currentDate) == 13) {
                        WTDModel wm = new WTDModel();
                         l.add(wm);
                        }
            }
            con.close();
        }catch(Exception e){
            System.out.println("Error " + e);
        }       
        return l;
}
```

SQL ส่วนการดึงข้อมูลทั้งหมด

```sql
String sql = "SELECT w.WTD_ID,w.WTD_NO,w.WTD_CREATE,w.WTD_PAY_DATE,w.EMP_ID,w.EMP_FNAME,w.EMP_SNAME "
                    + ",w.DIV_NAME,w.DEP_CODE,w.WTD_OBJ,w.REMARK "
                    + ",SUM(WTD_DTL_AMOUNT) AS SUM,CON_CREATE,CON_AMOUNT "
                    + "FROM PTY_DAT_WTD w "
                    + "INNER JOIN PTY_DAT_WTD_DTL ON PTY_DAT_WTD_DTL.WTD_ID=w.WTD_ID "
                    + "LEFT JOIN PTY_DAT_CON  ON PTY_DAT_CON.WTD_ID=w.WTD_ID "
                    + "WHERE w.WTD_TYPE1 = 'L' "
                    + "AND CON_CREATE IS NULL "
                    + "AND WTD_STATUS = '4' "
                    // filter all remark out
                    + "AND w.REMARK IS NULL "
                    + "GROUP BY w.WTD_ID,w.WTD_NO,w.WTD_CREATE,w.WTD_PAY_DATE,w.EMP_ID,"
                    + "w.EMP_FNAME,w.EMP_SNAME,w.DIV_NAME,w.DEP_CODE,w.WTD_OBJ,w.REMARK,"
                    + "CON_CREATE,CON_AMOUNT "
                    + "ORDER BY SUBSTR(w.DEP_CODE,1,2),w.WTD_PAY_DATE,w.EMP_FNAME,w.EMP_SNAME ";
```

ค่าที่ถูก set ลงจาก SQL

```java
wm.setFname(con.getResult().getString("EMP_FNAME"));
wm.setSname(con.getResult().getString("EMP_SNAME"));
wm.setWtdId(con.getResult().getString("WTD_ID"));
wm.setWtdNo(con.getResult().getString("WTD_NO"));
wm.setWtdCreateDate(con.getResult().getString("WTD_CREATE"));
wm.setWtdObjInt(con.getResult().getString("WTD_OBJ"));
wm.setWtdDtlAmount(con.getResult().getDouble("SUM"));
wm.setWtdPayDate(FormateDate.convertNVarcharDateOracleToWeb(con.getResult().getString("WTD_PAY_DATE")));
wm.setDivName(con.getResult().getString("DIV_NAME"));
wm.setDepCode(con.getResult().getString("DEP_CODE"));
wm.setWtdCreateDate(FormateDate.convertNVarcharDateOracleToWeb(con.getResult().getString("WTD_CREATE")));
wm.setAmount1(con.getResult().getDouble("CON_AMOUNT"));
wm.setConCreateDate(FormateDate.convertNVarcharDateOracleToWeb(con.getResult().getString("CON_CREATE")));
wm.setRemark(con.getResult().getString("REMARK"));
wm.setEmpId(con.getResult().getString("EMP_ID"));
//หาสังกัดฝ่ายเนื่องจาก ข้อมูลมาจากระบบ Web Sap cost center เป็นของแผนก
PerPerTab ObjDivcode = PerPerTab.getDivcod(con.getResult().getString("DEP_CODE"));
wm.setDepartment(ObjDivcode.getDivabb());
```

### Parameters

|Property|Type|Description|
|---|---|---|
|`currentDate`|`String`|Set currentDate from `FormateDate.now()`|
|`l`|`List<WTDModel>`|Return value|

## 🛠️ main()

ใน main function จะเป็นส่วนที่ code เริ่มทำงานโดยจะแบ่งเป็น 2 ส่วนตามแบบเมลล์

### 🔹เมลล์สำหรับคนที่ได้รับเงินมา 13 วัน

### Code Sample

```java
public static void main(){
        int statusEmpMailSend_13days = 1;
        int statusSuperiorEmpMailSend_13days = 1;
        int statusEmpMailSend_16days = 1;
        int statusSuperiorEmpMailSend_16days = 1;
        String type = "BEFORE";
        List<WTDModel> data = getData13days();
        System.out.println("Number of today's target (Before Deadline): "+data.size());
        if(data.size() > 0){
            for (WTDModel wm : data) {
                System.out.println("Target :"+wm.getEmpId()+" "+wm.getDepCode()+" ");
                String empMail = getEmpMail(wm.getEmpId());
                wm.setEmpMail(empMail);
                System.out.println("empMail = "+empMail);
                String supMail = getSuperiorEmpMail(wm);
                System.out.println("supMail = "+supMail);
                wm.setSupMail(supMail);
                statusEmpMailSend_13days = sendMail(wm,empMail,type,"EMP");
                statusSuperiorEmpMailSend_13days = sendMail(wm,supMail,type,"SUP");
                // send success = 2 , send failed = 1
                insertMailLog(wm,statusEmpMailSend_13days,statusSuperiorEmpMailSend_13days,"BEF");
            }       
        } else{
            System.out.println("No wtdId that meet requirements (13 days after wtd_pay_date)");
        }
        
```

### Parameters

|Property|Type|Description|
|---|---|---|
|`statusEmpMailSend_13days`|`int`|Set default as `1` which means fail. Change status by `sendMail()`|
|`statusEmpMailSend_13days`|`int`|Set default as `1` which means fail. Change status by `sendMail()`|
|`statusEmpMailSend_16days`|`int`|Set default as `1` which means fail. Change status by `sendMail()`|
|`statusSuperiorEmpMailSend_16days`|`int`|Set default as `1` which means fail. Change status by `sendMail()`|
|`type`|`String`|Set default as `“BEFORE”` in 13 days part.|
|`data`|`List<WTDModel>`|Recieve data from `getData13days()`|

### 🔹เมลล์สำหรับคนที่ได้รับเงินมาเกินกำหนด (15 วัน)

### Code Sample

```java
      	List<WTDModel> data2 = getDataMoreThan15days();
        type = "AFTER";
        System.out.println("Number of today's target (After deadline): "+data2.size());        
        if(data2.size() > 0){
            for (WTDModel wm : data) {
                System.out.println("Target :"+wm.getEmpId()+" "+wm.getDepCode()+" ");
                String empMail = getEmpMail(wm.getEmpId());
                System.out.println("empMail = "+empMail);
                wm.setEmpMail(empMail);
                String supMail = getSuperiorEmpMail(wm);
                System.out.println("supMail = "+supMail);
                wm.setSupMail(supMail);
                statusEmpMailSend_16days = sendMail(wm,empMail,type,"EMP");
                statusSuperiorEmpMailSend_16days= sendMail(wm,supMail,type,"SUP");
                // send success = 2 , send failed = 1
                insertMailLog(wm,statusEmpMailSend_16days,statusSuperiorEmpMailSend_16days,"AFT");
            }       
        } else{
            System.out.println("No wtdId that meet requirements (16 days after wtd_pay_date)");
        }
        
    }
```

### Parameters

| Property | Type             | Description                                 |
| -------- | ---------------- | ------------------------------------------- |
| `data2`  | `List<WTDModel>` | Recieve data from `getDataMoreThan15days()` |

![[/]]