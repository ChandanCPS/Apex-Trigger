trigger ContactCount on Contact (after insert, after delete) {
    // Write a trigger to count all Contacts of an Account and display them in Account...
    // increase/decrease the number as Contacts are deleted/added & also display Contact's total amount in Account.
    
    set<ID> setOfAccId = new set<ID>();
    if(trigger.new != null && !trigger.new.isEmpty()){
    for(Contact conObj: Trigger.new){
        setOfAccId.add(conObj.AccountId);
    }
}
    if(trigger.old != null && !trigger.old.isEmpty()){
    for(Contact conObj: Trigger.old){
        setOfAccId.add(conObj.AccountId);
    }
}
    
   List<Account> accListToAdd = new List<Account>();
    for(Account accQuery : [Select ID, Number_of_Contacts_del__c, Total_Contact_Price__c, (select ID, Contact_Price__c from Contacts) from Account where ID in:setOfAccId]){
        Decimal count =0;
        accQuery.Number_of_Contacts_del__c = accQuery.Contacts.size();
        for(Contact con : accQuery.contacts){
            count = count+ con.Contact_Price__c;
        }
        accQuery.Total_Contact_Price__c = count;
        accListToAdd.add(accQuery);
    }
    if(!accListToAdd.isEmpty()){
        UPDATE accListToAdd;
    }
    

}
