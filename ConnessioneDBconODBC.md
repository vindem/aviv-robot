Ecco a voi qualche piccola linea guida che spero vi siano utili in caso dobbiate mettere mano al codice per la connessione al DB.

Per prima cosa, tutto questo che sto per scrivere funziona SOLO con Visual Studio Professional, e NON con l'Express. Io uso la 2008, non so se e come funziona con la 2005..visto che la compatibilità tra una versione e l'altra non è mantenuta benissimo..

Una volta creato un progetto, di tipo Win32 console, accedere alle proprietà del progetto.
Andare nella sezione "Proprietà di configurazione - Generale" ed impostare "Uso MFC" come "Libreria DLL Condivisa".

gli header da includere sono i seguenti:

#include <afxdb.h>
#include <afx.h>
#include <odbcinst.h>

Le classi principali per creare, gestire connessioni e query sono queste:

> CDatabase cd: per aprire e chiudere connessioni
> CRecordset rs: Simile al ResultSet in Java, per eseguire query

un esempio di codice per stabilire una connessione è questo:

> LPCTSTR conStr = _T("Provider=Microsoft.ACE.OLEDB.12.0;Data Source=D:\Project\CPP\Prova\_DB \Debug\GestioneClienti.mdb;Persist Security Info=False;");_

> cd.Open(NULL, false, true, conStr, true);

la prima istruzione crea un LPCSTR, (_T, prende una stringa e restituisce sto LPCSTR) un qualche tipo di stringa particolare, rappresentante la connection string. Non capisco perchè le stringhe ordinarie non vanno bene a nessuno: infatti non ci sarà mai l'occasione di usare una semplice stringa._

Per quanto riguarda il metodo Open, i parametri sono i seguenti:


  1. LPCTSTR dsn: un nome di data source registrato in ODBC tramite l'ODBC Administrator program. Una volta capito come si crea, non ci sarà più bisogno di scegliere il percorso del DB a mano ogni volta.

> 2) BOOL bExclusive: non supportata in questa versione (che cacchio ce la mettono a fare? vabbè..compatibilità con le prossime versioni) DEVE essere "false", chissà perchè, invece di ignorarne il valore, se gli viene passato "true" c'è qualche problema.

> 3) BOOL bReadOnly: True se vi vuole aprire in read only, false altrimenti

> 4) LPCTSTR conStr: connection string

> 5) BOOL bUseCursorLib: True per caricare le DLL del cursore di ODBC. Meglio tenerlo sempre a True per non avere problemi :)


Un esempio di esecuzione di una query è questo:

> string query = "SELECT _FROM Modules Where nome = '" + nome + "'";
> rs.Open(AFX\_DB\_USE\_DEFAULT\_TYPE, **T(query), CRecordset::readOnly);**

i parametri della Open sono:_

  1. UINT nOpenType:     - AFX\_DB\_USE\_DEFAULT\_TYPE   oppure un elemento dell'enumerazione

> - CRecordset::dynaset : A recordset with bi-directional  scrolling. The membership and ordering of the records are determined when the recordset is opened, but changes made by other users to the data values are visible following a fetch operation. Dynasets are also known as keyset-driven recordsets.

> - CRecordset::snapshot : A static recordset with bi-directional scrolling. The membership and ordering of the records are determined when the recordset is opened; the data values are determined when the records are fetched. Changes made by other users are not visible until the recordset is closed and then reopened.

> - CRecordset::dynamic   A recordset with bi-directional scrolling. Changes made by other users to the membership, ordering, and data values are visible following a fetch operation. Note that many ODBC drivers do not support this type of recordset.

> - CRecordset::forwardOnly   A read-only recordset with only forward scrolling.


Scusate ma non mi andava di traddurre principalmente per 3 motivi: l'ora è quello che è; siamo adulti e vaccinati e possiamo capirlo bene; cosa più importante, dubito che ci servirà qualcosa di diverso dal valore di default o se proprio vogliamo il read only :)

> 2) LPCTSTR query: la stringa con la query

> 3) DWORD dwOptions:

> A bitmask which can specify a combination of the values listed below. Some of these are mutually exclusive. The default value is none.

> CRecordset::none   No options set. This parameter value is mutually exclusive with all other values. By default, the recordset can be updated with Edit or Delete and allows appending new records with AddNew. Updatability depends on the data source as well as on the nOpenType option you specify. Optimization for bulk additions is not available. Bulk row fetching will not be implemented. Deleted records will not be skipped during recordset navigation. Bookmarks are not available. Automatic dirty field checking is implemented.

> CRecordset::appendOnly   Do not allow Edit or Delete on the recordset. Allow AddNew only. This option is mutually exclusive with CRecordset::readOnly.

> CRecordset::readOnly   Open the recordset as read-only. This option is mutually exclusive with CRecordset::appendOnly.

> CRecordset::optimizeBulkAdd   Use a prepared SQL statement to optimize adding many records at one time. Applies only if you are not using the ODBC API function SQLSetPos to update the recordset. The first update determines which fields are marked dirty. This option is mutually exclusive with CRecordset::useMultiRowFetch.

> CRecordset::useMultiRowFetch   Implement bulk row fetching to allow multiple rows to be retrieved in a single fetch operation. This is an advanced feature designed to improve performance; however, bulk record field exchange is not supported by ClassWizard. This option is mutually exclusive with CRecordset::optimizeBulkAdd. Note that if you specify CRecordset::useMultiRowFetch, then the option CRecordset::noDirtyFieldCheck will be turned on automatically (double buffering will not be available); on forward-only recordsets, the option CRecordset::useExtendedFetch will be turned on automatically. For more information about bulk row fetching, see the article Recordset: Fetching Records in Bulk (ODBC).

> CRecordset::skipDeletedRecords   Skip all deleted records when navigating through the recordset. This will slow performance in certain relative fetches. This option is not valid on forward-only recordsets. If you call Move with the nRows parameter set to 0, and the CRecordset::skipDeletedRecords option set, Move will assert. Note that CRecordset::skipDeletedRecords is similar to driver packing, which means that deleted rows are removed from the recordset. However, if your driver packs records, then it will skip only those records that you delete; it will not skip records deleted by other users while the recordset is open. CRecordset::skipDeletedRecords will skip rows deleted by other users.

> CRecordset::useBookmarks   May use bookmarks on the recordset, if supported. Bookmarks slow data retrieval but improve performance for data navigation. Not valid on forward-only recordsets. For more information, see the article Recordset: Bookmarks and Absolute Positions (ODBC).

> CRecordset::noDirtyFieldCheck   Turn off automatic dirty field checking (double buffering). This will improve performance; however, you must manually mark fields as dirty by calling the SetFieldDirty and SetFieldNull member functions.Note that double buffering in class CRecordset is similar to double buffering in class CDaoRecordset. However, in CRecordset, you cannot enable double buffering on individual fields; you either enable it for all fields or disable it for all fields. Note that if you specify the option CRecordset::useMultiRowFetch, then CRecordset::noDirtyFieldCheck will be turned on automatically; however, SetFieldDirty and SetFieldNull cannot be used on recordsets that implement bulk row fetching.

> CRecordset::executeDirect   Do not use a prepared SQL statement. For improved performance, specify this option if the Requery member function will never be called.

> CRecordset::useExtendedFetch   Implement SQLExtendedFetch instead of SQLFetch. This is designed for implementing bulk row fetching on forward-only recordsets. If you specify the option CRecordset::useMultiRowFetch on a forward-only recordset, then CRecordset::useExtendedFetch will be turned on automatically.

> CRecordset::userAllocMultiRowBuffers   The user will allocate storage buffers for the data. Use this option in conjunction with CRecordset::useMultiRowFetch if you want to allocate your own storage; otherwise, the framework will automatically allocate the necessary storage. For more information, see the article Recordset: Fetching Records in Bulk (ODBC). Note that specifying CRecordset::userAllocMultiRowBuffers without specifying CRecordset::useMultiRowFetch will result in a failed assertion.


come prima, visto che dobbiamo fare solo un paio di select e un paio di inserimenti credo che si possa usare sempre il "none".


Alcuni metodi utili per i recordset:

> int rs.GetRecordCount() : restituisce il numero di righe

> void rs.MoveFirst() : sposta il cursore alla prima riga
> > rs.MoveLast(), moveNext() e simili spostano il cursore..


> void rs.GetFieldValue(_T("NomeCampo"), varValue):
> > legge il valore del campo specificato e lo mette in varValue. Indipendentemente dal tipo._


CDBVariant varValue: oggetto che può essere riempito con diversi tipi di dati, tra cui:
varValue.m\_lVal per i long;
varValue.m\_pstring per le stringhe, dovrebbe essere un CString, ancora non capisco perchè non usano stringhe normali..indagherò.


Per ora è tutto..scusate l'orrenda impaginazione.
So che è abbastanza confuso..ma nei prossimi giorni scrivendo e facendo prove mi chiarirò le idee e aggiornerò il wiki :)