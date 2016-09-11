# CSV-Transaction-Generator
Extract all CSV transactions when a CSV file URL is given.
    
##Where it is useful?
 - Useful in processing files of CSV format where file has multiple transactions of miscleneous types.
 - Best use case is processing of CSV response files in Electronic data interchange.
    
##How it is useful?    
  - Eliminates boiler plate,complex logic for parsing CSV,extracting transaction data of variable length and types with different transaction identifiers in same file.
  - Just specify list of fileTypes,transactionTypes,transactionHeaders,transactionIdentifer locations rest of parsing and extracting transaction data is automated.

## Documentation
  For example consider the following CSV file which is of type CFR and has one CFRCancellation(T06) transactions,two
  CFRConfirmationAccept(S07) transactions.
  
```
"A00","101","CFR","20160420","113426",3
"S07",11111,2,1183344,"FFK-331",20160420,"FIRM","TRAN","M",1700,170,1700,170,1500,445,113,123456.78,123456.78,123456.78,123456.78,"EX1","LD4",34,"DMA",1,"Y","Mr Oliver Cromwell","Mr Oliver Cromwell",102,"Y",20160420,093927,"D","Y"
"S66","CON","Mr","Oliver Cromwell",,"Oliver Cromwell",,"20160420"
"S67","TEL","2939399399"
"S84","04"
"S70","3","","","HIGH STREET","CRAFTY VALLEY","BIG CITY","AA1","1AA"
"S75",00111111111111,03,"1107738116S","DM",4000,"21S88934",675,"P",,20160420,"",05,"Y"
"K12","S","UNKNOWN","UNKNOWN",20160420
"K14","T","G2500","3839",1987,"N",100,"102M12","388",1987,100,1000
T06,RJ,22222,FFK-331,222222222222222,1,,,
"S07",22222,2,1183344,"FFK-331",20160420,"FIRM","TRAN","M",1700,170,1700,170,1500,445,113,123456.78,123456.78,123456.78,123456.78,"EX1","LD4",34,"DMA",1,"Y","Mr Oliver Cromwell","Mr Oliver Cromwell",102,"Y",20160420,093927,"D","Y"
"S66","CON","Mr","Oliver Cromwell",,"Oliver Cromwell",,"20160420"
"S67","TEL","2939399399"
"S84","04"
"S70","3","","","HIGH STREET","CRAFTY VALLEY","BIG CITY","AA1","1AA"
"S75",00111111111111,03,"1107738116S","DM",4000,"21S88934",675,"P",,20160420,"",05,"Y"
"K12","S","UNKNOWN","UNKNOWN",20160420
"K14","T","G2500","3839",1987,"N",100,"102M12","388",1987,100,1000
"Z99",8
```
When this file url is input to CSV-Transaction-Generator,output is,

```

EDITransactionDetails 
[transactionData=T06,RJ,22222,FFK-331,222222222222222,1, , , ,#, 
transactionIdentifier=222222222222222, 
csvHierarchy=CFR, 
ediTransactionType=CFR_CANCELLATION]

EDITransactionDetails [transactionData=S07,11111,2,1183344,FFK-331,20160420,FIRM,TRAN,M,1700,170,1700,170,1500,445,113,123456.78,123456.78,123456.78,123456.78,EX1,LD4,34,DMA,1,Y,Mr Oliver Cromwell,Mr Oliver Cromwell,102,Y,20160420,093927,D,Y,#S66,CON,Mr,Oliver Cromwell, ,Oliver Cromwell, ,20160420,#S67,TEL,2939399399,#S84,04,#S70,3, , ,HIGH STREET,CRAFTY VALLEY,BIG CITY,AA1,1AA,#S75,00111111111111,03,1107738116S,DM,4000,21S88934,675,P, ,20160420, ,05,Y,#K12,S,UNKNOWN,UNKNOWN,20160420,#K14,T,G2500,3839,1987,N,100,102M12,388,1987,100,1000,#, 
transactionIdentifier=00111111111111, 
csvHierarchy=CFR, 
ediTransactionType=CFR_CANCELLATION]

EDITransactionDetails [transactionData=S07,22222,2,1183344,FFK-331,20160420,FIRM,TRAN,M,1700,170,1700,170,1500,445,113,123456.78,123456.78,123456.78,123456.78,EX1,LD4,34,DMA,1,Y,Mr Oliver Cromwell,Mr Oliver Cromwell,102,Y,20160420,093927,D,Y,#S66,CON,Mr,Oliver Cromwell, ,Oliver Cromwell, ,20160420,#S67,TEL,2939399399,#S84,04,#S70,3, , ,HIGH STREET,CRAFTY VALLEY,BIG CITY,AA1,1AA,#S75,00111111111111,03,1107738116S,DM,4000,21S88934,675,P, ,20160420, ,05,Y,#K12,S,UNKNOWN,UNKNOWN,20160420,#K14,T,G2500,3839,1987,N,100,102M12,388,1987,100,1000,#, 
transactionIdentifier=00111111111111, 
csvHierarchy=CFR, 
ediTransactionType=CFR_CANCELLATION]



```
##Usage
  * Open __CSVFile.java__ add your lineItem identifiers in it.For example,in above case,add S07,S70,S75,K12,K14 etc.,
  * Open __CSVHierarchy.java__ and add your CSV file types.For example,in above case CFR is added.
  * Open __EdiTransactionType.java__ and add your CSV transaction types.For example,in above case CFR_CANCEL is added.
  * Open __EDIFileType.java__ and add your CSV file group type.For example,in above case SPA is added.
  * Open __CSVTransactionAttributes.java__ and add transactionDetails.For example,In CFR file,we get any number of CFRConfirmationAccept(S07),ConfirmationCancellation(T06),CFRConfirmationReject(S16)
     So,we need to add in getResponseFileDetails(List<String[]> csvRows) as follows.
      ```
      case S07: {
				responseCSVHierarchy = CSVHierarchy.CFR;
				responseFileHeader = CSVFile.A00;
				responseFileFooter = CSVFile.Z99;
				partitioningHeader1 = CSVFile.S07;
				partitioningHeader2 = CSVFile.S16;
				partitioningHeader3 = CSVFile.T06;
				responseCSVTransactionType = EdiTransactionType.CFR_ACCEPTED_CONFIRMATION;
				ediResponseType = EdiFileType.SPA;
				
				break;
			}
			case T06: {
				responseCSVHierarchy = CSVHierarchy.CFR;
				responseFileHeader = CSVFile.A00;
				responseFileFooter = CSVFile.Z99;
				partitioningHeader1 = CSVFile.S07;
				partitioningHeader2 = CSVFile.S16;
				partitioningHeader3 = CSVFile.T06;
				responseCSVTransactionType = EdiTransactionType.CFR_CANCELLATION;
				ediResponseType = EdiFileType.SPA;

				break;
			}
			case S16: {
				responseCSVHierarchy = CSVHierarchy.CFR;
				responseFileHeader = CSVFile.A00;
				responseFileFooter = CSVFile.Z99;
				partitioningHeader1 = CSVFile.S07;
				partitioningHeader2 = CSVFile.S16;
				partitioningHeader3 = CSVFile.T06;
				responseCSVTransactionType = EdiTransactionType.CFR_REJECTED_CONFIRMATION;
				ediResponseType = EdiFileType.SPA;
			
				break;
			}
      ```
    * For example,In CFRAcceptedConfirmation(S07) transaction,Transaction identifier is present in second position of S75 line item.
    So,update getMPRNFileHeader(String[] responseCSVHeader, String[] mPRNStartFile) as follows.
     ```
    case S07: {

				mprnFileHeader = CSVFile.S75;
				mprnPositionIndex = 1;
				isValidMPRNSegment = Constants.YES;
				break;
			}
     ```
     Similarly,update for other transaction types

##License
  ```
         Copyright 2016 Poonganam Lakshmi Sravanthi

           Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
```
    
  

