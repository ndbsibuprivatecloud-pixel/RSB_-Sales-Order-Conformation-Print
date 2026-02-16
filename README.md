# RSB_-Sales-Order-Conformation-Print
Sales Order Conformation Print
Classes 

class ZCL_ZSALES_ORDER_CONFO_DPC definition
  public
  inheriting from CL_FDP_V1_ORDER_CONFIR_DPC_EXT
  abstract
  create public .

public section.
protected section.
private section.
ENDCLASS.



CLASS ZCL_ZSALES_ORDER_CONFO_DPC IMPLEMENTATION.
ENDCLASS.
*********************************************************************************

class ZCL_ZSALES_ORDER_CONFO_DPC_EXT definition
  public
  inheriting from ZCL_ZSALES_ORDER_CONFO_DPC
  create public .

public section.

  constants SALES_ORDER type TDOBNAME value 'SalesOrder' ##NO_TEXT.
  constants DASH type CHAR3 value '-' ##NO_TEXT.
  constants COMA type CHAR3 value ',' ##NO_TEXT.
  constants HEADERSET type STRING value 'SO_HeaderSet' ##NO_TEXT.
  constants ITEMSET type STRING value 'SO_ItemSet' ##NO_TEXT.
  constants ONLY type CHAR4 value 'ONLY' ##NO_TEXT.
  constants PERCENTAGE type CHAR4 value '%' ##NO_TEXT.
  constants ITEMSETWITHOUTDISCOUNT type STRING value 'SO_Item_Without_DiscountSet' ##NO_TEXT.

  methods /IWBEP/IF_MGW_APPL_SRV_RUNTIME~GET_ENTITY
    redefinition .
  methods /IWBEP/IF_MGW_APPL_SRV_RUNTIME~GET_ENTITYSET
    redefinition .
protected section.

  methods HEADER_GET_ENTITY
    importing
      !IV_ENTITY_NAME type STRING
      !IV_ENTITY_SET_NAME type STRING
      !IV_SOURCE_NAME type STRING
      !IT_KEY_TAB type /IWBEP/T_MGW_NAME_VALUE_PAIR
      !IO_REQUEST_OBJECT type ref to /IWBEP/IF_MGW_REQ_ENTITY optional
      !IO_TECH_REQUEST_CONTEXT type ref to /IWBEP/IF_MGW_REQ_ENTITY optional
      !IT_NAVIGATION_PATH type /IWBEP/T_MGW_NAVIGATION_PATH
    exporting
      !ER_ENTITY type ZCL_ZSALES_ORDER_CONFO_MPC=>TS_SO_HEADER
      !ES_RESPONSE_CONTEXT type /IWBEP/IF_MGW_APPL_SRV_RUNTIME=>TY_S_MGW_RESPONSE_ENTITY_CNTXT
    raising
      /IWBEP/CX_MGW_BUSI_EXCEPTION
      /IWBEP/CX_MGW_TECH_EXCEPTION .
  methods ITEMS_GET_ENTITYSET
    importing
      !IV_ENTITY_NAME type STRING
      !IV_ENTITY_SET_NAME type STRING
      !IV_SOURCE_NAME type STRING
      !IT_FILTER_SELECT_OPTIONS type /IWBEP/T_MGW_SELECT_OPTION
      !IS_PAGING type /IWBEP/S_MGW_PAGING
      !IT_KEY_TAB type /IWBEP/T_MGW_NAME_VALUE_PAIR
      !IT_NAVIGATION_PATH type /IWBEP/T_MGW_NAVIGATION_PATH
      !IT_ORDER type /IWBEP/T_MGW_SORTING_ORDER
      !IV_FILTER_STRING type STRING
      !IV_SEARCH_STRING type STRING
      !IO_TECH_REQUEST_CONTEXT type ref to /IWBEP/IF_MGW_REQ_ENTITYSET optional
    exporting
      !ET_ENTITYSET type ZCL_ZSALES_ORDER_CONFO_MPC=>TT_SO_ITEM
      !ES_RESPONSE_CONTEXT type /IWBEP/IF_MGW_APPL_SRV_RUNTIME=>TY_S_MGW_RESPONSE_CONTEXT
    raising
      /IWBEP/CX_MGW_BUSI_EXCEPTION
      /IWBEP/CX_MGW_TECH_EXCEPTION .
  methods ITEMS_NO_DISC_GET_ENTITYSET
    importing
      !IV_ENTITY_NAME type STRING
      !IV_ENTITY_SET_NAME type STRING
      !IV_SOURCE_NAME type STRING
      !IT_FILTER_SELECT_OPTIONS type /IWBEP/T_MGW_SELECT_OPTION
      !IS_PAGING type /IWBEP/S_MGW_PAGING
      !IT_KEY_TAB type /IWBEP/T_MGW_NAME_VALUE_PAIR
      !IT_NAVIGATION_PATH type /IWBEP/T_MGW_NAVIGATION_PATH
      !IT_ORDER type /IWBEP/T_MGW_SORTING_ORDER
      !IV_FILTER_STRING type STRING
      !IV_SEARCH_STRING type STRING
      !IO_TECH_REQUEST_CONTEXT type ref to /IWBEP/IF_MGW_REQ_ENTITYSET optional
    exporting
      !ET_ENTITYSET type ZCL_ZSALES_ORDER_CONFO_MPC=>TT_SO_ITEM_WITHOUT_DISCOUNT
      !ES_RESPONSE_CONTEXT type /IWBEP/IF_MGW_APPL_SRV_RUNTIME=>TY_S_MGW_RESPONSE_CONTEXT
    raising
      /IWBEP/CX_MGW_BUSI_EXCEPTION
      /IWBEP/CX_MGW_TECH_EXCEPTION .
private section.

  methods GET_ITEMS
    importing
      !SALES_ORDER type VBELN
    exporting
      !ITEMS_WITHOUT_DISCOUNT type ZCL_ZSALES_ORDER_CONFO_MPC=>TT_SO_ITEM_WITHOUT_DISCOUNT
      !ITEMS_WITH_DISCOUNT type ZCL_ZSALES_ORDER_CONFO_MPC=>TT_SO_ITEM .
ENDCLASS.



CLASS ZCL_ZSALES_ORDER_CONFO_DPC_EXT IMPLEMENTATION.


  METHOD /iwbep/if_mgw_appl_srv_runtime~get_entity.
*----------------------------------------------------------------------*
* Report  ZCL_ZSALES_ORDER_CONFO_DPC_EXT                               *
*----------------------------------------------------------------------*
* Title:          Sales Order Conformation                             *
* RICEF#:         SD 09                                                *
* Transaction:    VA02                                                 *
*----------------------------------------------------------------------*
* Copyright:      NDBS, Inc.                                           *
* Client:         RSB Transmission                                     *
*----------------------------------------------------------------------*
* Developer:      Asafwar Shivam                                       *
* Creation Date:  30-Jul-2025                                          *
*----------------------------------------------------------------------*


    DATA: poheadtohead        TYPE TABLE OF zcl_zsales_order_confo_mpc_ext=>ts_so_header.
    DATA(entityset) = io_tech_request_context->get_entity_set_name( ).

    CASE entityset.
      WHEN headerset.    "'SO_HeaderSet'

        "  calling Header method
        TRY.
            header_get_entity(
              EXPORTING
                iv_entity_name          = iv_entity_name
                iv_entity_set_name      = iv_entity_set_name
                iv_source_name          = iv_source_name
                it_key_tab              = it_key_tab                              " table for name value pairs
                io_request_object       = io_tech_request_context                 " Request Details for Entity Read Operation
                io_tech_request_context = io_tech_request_context                 " Request Details for Entity Read Operation
                it_navigation_path      = it_navigation_path                      " table of navigation paths
              IMPORTING
                er_entity               = DATA(purchase_contract)
*                es_response_context     =                  " Feed request response information (Entity)
            ).
          CATCH /iwbep/cx_mgw_busi_exception. " Business Exception
          CATCH /iwbep/cx_mgw_tech_exception. " Technical Exception
        ENDTRY.
        IF purchase_contract IS NOT INITIAL.
          copy_data_to_ref( EXPORTING is_data = purchase_contract
                            CHANGING cr_data = er_entity ).
          CLEAR : purchase_contract.
        ENDIF.

      WHEN OTHERS.

        "   Calling Standard Method
        TRY.
            CALL METHOD super->/iwbep/if_mgw_appl_srv_runtime~get_entity
              EXPORTING
                iv_entity_name          = iv_entity_name
                iv_entity_set_name      = iv_entity_set_name
                iv_source_name          = iv_source_name
                it_key_tab              = it_key_tab
*               io_request_object       = IO_TECH_REQUEST_CONTEXT
                io_tech_request_context = io_tech_request_context
                it_navigation_path      = it_navigation_path
              IMPORTING
                er_entity               = er_entity.
          CATCH /iwbep/cx_mgw_busi_exception. " business exception in mgw
          CATCH /iwbep/cx_mgw_tech_exception. " mgw technical exception
        ENDTRY.
    ENDCASE.

  ENDMETHOD.


  METHOD /iwbep/if_mgw_appl_srv_runtime~get_entityset.
*----------------------------------------------------------------------*
* Report  ZCL_ZSALES_ORDER_CONFO_DPC_EXT                               *
*----------------------------------------------------------------------*
* Title:          Sales Order Conformation                             *
* RICEF#:         SD 09                                                *
* Transaction:    VA02                                                 *
*----------------------------------------------------------------------*
* Copyright:      NDBS, Inc.                                           *
* Client:         RSB Transmission                                     *
*----------------------------------------------------------------------*
* Developer:      Asafwar Shivam                                       *
* Creation Date:  30-Jul-2025                                          *
*----------------------------------------------------------------------*


    DATA(entityset) = io_tech_request_context->get_entity_set_name( ).

    "  calling items method
    IF  entityset = itemset.                "'SO_ItemSet'.
      TRY.
          CALL METHOD items_get_entityset
            EXPORTING
              iv_entity_name           = iv_entity_name
              iv_entity_set_name       = iv_entity_set_name
              iv_source_name           = iv_source_name
              it_filter_select_options = it_filter_select_options                 " Table of select options
              is_paging                = is_paging                                " Paging structure
              it_key_tab               = it_key_tab                               " Table for name value pairs
              it_navigation_path       = it_navigation_path                       " Table of navigation paths
              it_order                 = it_order                                 " The sorting order
              iv_filter_string         = iv_filter_string                         " Table for name value pairs
              iv_search_string         = iv_search_string
            IMPORTING                 "  MPORTING
              et_entityset             = DATA(subcon_delivery_details)                             " Returning data
              es_response_context      = es_response_context.
        CATCH /iwbep/cx_mgw_busi_exception. " business exception in mgw
        CATCH /iwbep/cx_mgw_tech_exception. " mgw technical exception
      ENDTRY.

      IF subcon_delivery_details IS NOT INITIAL.
        copy_data_to_ref( EXPORTING is_data = subcon_delivery_details
                          CHANGING cr_data = er_entityset ).
        CLEAR : subcon_delivery_details.
      ENDIF.

      "  calling items method for without discount values
    ELSEIF  entityset = itemsetwithoutdiscount.                "'SO_ItemSet'.
      TRY.
          CALL METHOD items_no_disc_get_entityset
            EXPORTING
              iv_entity_name           = iv_entity_name
              iv_entity_set_name       = iv_entity_set_name
              iv_source_name           = iv_source_name
              it_filter_select_options = it_filter_select_options                 " Table of select options
              is_paging                = is_paging                                " Paging structure
              it_key_tab               = it_key_tab                               " Table for name value pairs
              it_navigation_path       = it_navigation_path                       " Table of navigation paths
              it_order                 = it_order                                 " The sorting order
              iv_filter_string         = iv_filter_string                         " Table for name value pairs
              iv_search_string         = iv_search_string
            IMPORTING                 "  MPORTING
              et_entityset             = DATA(subcon_delivery_no_discount)                             " Returning data
              es_response_context      = es_response_context.
        CATCH /iwbep/cx_mgw_busi_exception. " business exception in mgw
        CATCH /iwbep/cx_mgw_tech_exception. " mgw technical exception
      ENDTRY.

      IF subcon_delivery_no_discount IS NOT INITIAL.
        copy_data_to_ref( EXPORTING is_data = subcon_delivery_no_discount
                          CHANGING cr_data = er_entityset ).
        CLEAR : subcon_delivery_no_discount.
      ENDIF.

      "   Calling Standard Method
    ELSE.
      TRY.
          CALL METHOD super->/iwbep/if_mgw_appl_srv_runtime~get_entityset
            EXPORTING
              iv_entity_name           = iv_entity_name                           " Obsolete
              iv_entity_set_name       = iv_entity_set_name                       " Obsolete
              iv_source_name           = iv_source_name                           " Obsolete
              it_filter_select_options = it_filter_select_options                 " table of select options - Obsolete
              it_order                 = it_order                                 " the sorting order - Obsolete
              is_paging                = is_paging                                " paging structure - Obsolete
              it_navigation_path       = it_navigation_path                       " table of navigation paths - Obsolete
              it_key_tab               = it_key_tab                               " table for name value pairs - Obsolete
              iv_filter_string         = iv_filter_string                         " the filter as a string containing ANDs and ORs etc -Obsolete
              iv_search_string         = iv_search_string                         " Obsolete
              io_tech_request_context  = io_tech_request_context
            IMPORTING
              er_entityset             = er_entityset
              es_response_context      = es_response_context.

        CATCH /iwbep/cx_mgw_busi_exception. " business exception in mgw
        CATCH /iwbep/cx_mgw_tech_exception. " mgw technical exception
      ENDTRY.

    ENDIF.

  ENDMETHOD.


METHOD header_get_entity.
*----------------------------------------------------------------------*
* Report  ZCL_ZSALES_ORDER_CONFO_DPC_EXT                               *
*----------------------------------------------------------------------*
* Title:          Sales Order Conformation                             *
* RICEF#:         SD 09                                                *
* Transaction:    VA02                                                 *
*----------------------------------------------------------------------*
* Copyright:      NDBS, Inc.                                           *
* Client:         RSB Transmission                                     *
*----------------------------------------------------------------------*
* Developer:      Asafwar Shivam                                       *
* Creation Date:  30-Jul-2025                                          *
*----------------------------------------------------------------------*


  "   Data Declaration For Purchase Order.
  DATA(sales_order) = VALUE #( it_key_tab[ name = sales_order ]-value OPTIONAL ).
  DATA : lt_j_1bt001wv TYPE TABLE OF j_1bt001wv,
         bt_customer   TYPE bu_partner,
         sf_plant      TYPE bu_partner,
         st_customer   TYPE bu_partner,
         name          TYPE thead-tdname.

  "  Fetching Sales Details
  SELECT SINGLE
      sales_header~salesdocument AS invoice,
      sales_header~creationdate AS invoice_date,
      sales_document~customerpaymentterms AS payment_terms,
      sales_document~incotermsclassification AS inco1,
      sales_document~incotermstransferlocation AS inco2,
      sales_item~plant AS bf_plant,
      sales_header~billingcompanycode AS company_code,
      sales_header~soldtoparty AS customer,
      business_partner~customer AS ship_to_customer,
    company_code~companycodename AS beneficiary_name
    FROM i_salesdocumentbasic AS sales_header
    LEFT OUTER JOIN i_salesdocumentitembasic AS sales_item
                                             ON sales_item~salesdocument = sales_header~salesdocument
    LEFT OUTER JOIN i_salesdocument AS sales_document
                                    ON sales_document~salesdocument = sales_header~salesdocument
    LEFT OUTER JOIN i_sddocumentcompletepartners AS business_partner
                                                 ON business_partner~sddocument = sales_header~salesdocument
                                                AND business_partner~partnerfunction = @text-005       " SH equal to 'WE'
    LEFT OUTER JOIN i_companycode AS company_code
                                  ON company_code~companycode = sales_header~billingcompanycode
    WHERE sales_header~salesdocument = @sales_order
    INTO @DATA(sales).

  IF sy-subrc = 0.
    "  Fetching Bill From Pan & Iec Details
    SELECT company_info~companycodeparametervalue AS pan,
           company_info~companycodeparametertype AS type
    FROM i_addlcompanycodeinformation AS company_info
    WHERE company_info~companycode = @sales-company_code
      AND company_info~companycodeparametertype IN ( @text-001, @text-002 )     "  'J_1I02'  'IEC'
    INTO TABLE @DATA(bf_iec_pan).

    IF sy-subrc = 0.
      "  Do Nothing
    ELSE.
      "  Do Nothing
    ENDIF.
  ENDIF.

  "  Fetching Bill From Payment Terms Details
  SELECT SINGLE
      payment_term_text~customerpaymentterms,
      payment_term_text~customerpaymenttermsname AS bf_pay_term_text
  FROM i_customerpaymenttermstext AS payment_term_text
                               WHERE payment_term_text~customerpaymentterms = @sales-payment_terms
                                 AND payment_term_text~language = @sy-langu
  INTO @DATA(payment_term_text).

  IF sy-subrc = 0.
    "  Do Nothing
  ELSE.
    "  Do Nothing
  ENDIF.

  "  Fetching Bill From Address Details
  SELECT SINGLE
    plant~addressid,
    address~addresseename1 AS name1,
    address~addresseename2 AS name2,
    address~streetname,
    address~cityname AS city1,
    address~districtname AS city2,
    address~region,
    region_text~regionname,
    address~country,
    address~postalcode AS post_code1
  FROM i_plant AS plant
  LEFT OUTER JOIN i_addrorgnamepostaladdress WITH PRIVILEGED ACCESS AS address
                                             ON address~addressid = plant~addressid
  LEFT OUTER JOIN i_excisetaxregiontext AS region_text
                                        ON region_text~region = address~region
                                       AND region_text~language = @sy-langu
                                       AND region_text~country = @text-004    " IN
  WHERE plant~plant = @sales-bf_plant
  INTO @DATA(bf_address).

  IF sy-subrc = 0.
    "  Do Nothing
  ELSE.
    "  Do Nothing
  ENDIF.

  "  Fetching Bill From Gstin Details
  CALL FUNCTION 'VIEW_GET_DATA'
    EXPORTING
      view_name = 'J_1BT001WV'
    TABLES
      data      = lt_j_1bt001wv.

  IF sy-subrc = 0.

    SELECT SINGLE                                      "#EC CI_BUFFJOIN
       business_places~j_1bbranch,
       gstin~gstin AS bf_gstin
    FROM  @lt_j_1bt001wv AS business_places
    LEFT OUTER JOIN j_1bbranch AS gstin
                               ON gstin~branch = business_places~j_1bbranch
    WHERE business_places~werks = @sales-bf_plant
    INTO @DATA(bf_gstin).

    IF sy-subrc = 0.
      "  Do Nothing
    ELSE.
      "  Do Nothing
    ENDIF.

  ENDIF.


  "  Fetching Bill To Address Details
  SELECT SINGLE
      customer~adrnr,
      address~addresseename1 AS name1,
      address~addresseename2 AS name2,
      address~streetname,
      address~cityname AS city1,
      address~districtname AS city2,
      address~region,
      region_text~regionname,
      address~country,
      address~postalcode AS post_code1,
      customer~j_1ipanno AS bt_pan
    FROM kna1 AS customer
    LEFT OUTER JOIN i_addrorgnamepostaladdress WITH PRIVILEGED ACCESS AS address
                                               ON address~addressid = customer~adrnr
    LEFT OUTER JOIN i_excisetaxregiontext AS region_text
                                          ON region_text~region = address~region
                                         AND region_text~language = @sy-langu
                                         AND region_text~country = @text-004     " IN
    WHERE customer~kunnr = @sales-customer
    INTO @DATA(bt_address).

  IF sy-subrc = 0.
    "  Do Nothing
  ELSE.
    "  Do Nothing
  ENDIF.

  "  Fetching Bill To Gstin Details
  bt_customer = sales-customer.
  bt_customer = |{ bt_customer ALPHA = IN }|.
  SELECT SINGLE
    tax_number~bptaxnumber,
    tax_number~businesspartner AS customer
  FROM i_businesspartnertaxnumber AS tax_number
  WHERE tax_number~businesspartner = @bt_customer
  AND tax_number~bptaxtype = @text-003
  INTO @DATA(bt_tax_number).

  IF sy-subrc = 0.
    "  Do Nothing
  ELSE.
    "  Do Nothing
  ENDIF.


  "  Fetching Ship From Address Details
  SELECT SINGLE
    plant~plant,
    address~addresseename1 AS name1,
    address~addresseename2 AS name2,
    address~streetname,
    address~cityname AS city1,
    address~districtname AS city2,
    address~region,
    region_text~regionname,
    address~country,
    address~postalcode AS post_code1,
    billingheader~zz1_shipfrom_bdh AS sf_plant,
    billingheader~zz1_housebankid_bdh AS bank_id
  FROM i_billingdocumentitembasic AS billingitem
  LEFT OUTER JOIN i_billingdocumentbasic AS billingheader
                                         ON billingheader~billingdocument = billingitem~billingdocument
  LEFT OUTER JOIN i_plant AS plant
                          ON plant~plant = billingheader~zz1_shipfrom_bdh
  LEFT OUTER JOIN i_addrorgnamepostaladdress WITH PRIVILEGED ACCESS AS address
                                                                    ON address~addressid = plant~addressid
  LEFT OUTER JOIN i_excisetaxregiontext AS region_text
                                        ON region_text~region = address~region
                                       AND region_text~language = @sy-langu
                                       AND region_text~country = @text-004      " IN
  WHERE billingitem~salesdocument = @sales_order
  INTO @DATA(sf_address).

  IF sy-subrc = 0.

    "  Fetching Ship From Gstin Details
    sf_plant = sf_address-sf_plant.
    sf_plant = |{ sf_plant ALPHA = IN }|.

    SELECT SINGLE
      tax_number~bptaxnumber,
      tax_number~businesspartner AS customer
    FROM i_businesspartnertaxnumber AS tax_number
    WHERE tax_number~businesspartner = @sf_plant
      AND tax_number~bptaxtype = @text-003
    INTO @DATA(sf_tax_number).

    IF sy-subrc = 0.
      "  Do Nothing
    ELSE.
      "  Do Nothing
    ENDIF.

  ENDIF.

  "  Fetching Ship To Address Details
  SELECT SINGLE
      customer~adrnr,
      address~addresseename1 AS name1,
      address~addresseename2 AS name2,
      address~streetname,
      address~cityname AS city1,
      address~districtname AS city2,
      address~region,
      region_text~regionname,
      address~country,
      address~postalcode AS post_code1,
      customer~j_1ipanno AS st_pan
    FROM kna1 AS customer
    LEFT OUTER JOIN i_addrorgnamepostaladdress WITH PRIVILEGED ACCESS AS address
                                               ON address~addressid = customer~adrnr
    LEFT OUTER JOIN i_excisetaxregiontext AS region_text
                                          ON region_text~region = address~region
                                         AND region_text~language = @sy-langu
                                         AND region_text~country = @text-004     " IN
    WHERE customer~kunnr = @sales-ship_to_customer
    INTO @DATA(st_address).

  IF sy-subrc = 0.

    "  Fetching Ship To Gstin Details
    st_customer = sales-ship_to_customer.
    st_customer = |{ st_customer ALPHA = IN }|.

    SELECT SINGLE
      tax_number~bptaxnumber,
      tax_number~businesspartner AS customer
    FROM i_businesspartnertaxnumber AS tax_number
    WHERE tax_number~businesspartner = @st_customer
    INTO @DATA(st_tax_number).

    IF sy-subrc = 0.
      "  Do Nothing
    ELSE.
      "  Do Nothing
    ENDIF.

  ENDIF.

**==> Start of changes by Vaishnavi Vairagre from 09.01.2025 as suggusted by Narender
  SELECT SINGLE
        vbeln,
        zz1_housebankid_sdh FROM vbak INTO @DATA(vbak_data)
        WHERE vbeln = @sales_order.
  IF sy-subrc = 0.

    "  Fetching Bank Details
    SELECT SINGLE
      house_bank_text~text1            AS bank_name,
      bank_branch~branch               AS bank_address,
      account_no~bankn                 AS account_no,
      house_bank~bankinternalid        AS ifsc_code,
      account_type~bankaccounttype     AS account_type,
      account_type~bankaccounttypetext AS account_type_desc
    FROM  i_housebankmaster AS house_bank
    LEFT OUTER JOIN t012t AS house_bank_text
                          ON house_bank~housebank = house_bank_text~hbkid
    LEFT OUTER JOIN t012k AS account_no
                          ON account_no~hbkid = house_bank_text~hbkid
    LEFT OUTER JOIN i_bank_2 AS bank_branch
                             ON bank_branch~bank = account_no~bankn
    LEFT OUTER JOIN fclm_bam_amd AS bank_account_master
                                 ON bank_account_master~acc_num  = account_no~bankn
    LEFT OUTER JOIN i_bankaccounttype AS account_type
                                      ON account_type~bankaccounttype = bank_account_master~acc_type_id
                                     AND account_type~language        = @sy-langu
    WHERE house_bank~housebank = @vbak_data-zz1_housebankid_sdh
*    WHERE house_bank~housebank = @sf_address-bank_id
    INTO @DATA(bank_details).

    IF sy-subrc = 0.
      "  Do Nothing
    ELSE.
      "  Do Nothing
    ENDIF.
  ENDIF.

  "  Fetching Remarks Field Data
  name = |{ sales_order ALPHA = IN }|.
  yglobalutility=>readtext(
    EXPORTING
      iv_textid  = TEXT-019    " ZREM
      iv_name    = name
      iv_textobj = TEXT-020    " VBBK
    IMPORTING
      ev_text    = DATA(remarks) ).

  IF sy-subrc = 0.
    "  Do Nothing
  ELSE.
    "  Do Nothing
  ENDIF.

  "  Populating Data
  er_entity-salesorder   = sales-invoice.
  er_entity-invoicedate  = sales-invoice_date.
  er_entity-paymentterms = sales-payment_terms.
  er_entity-paymentterms = condense( |{ sales-payment_terms }{ COND #( WHEN sales-payment_terms IS NOT INITIAL AND
                                                                            payment_term_text-bf_pay_term_text IS NOT INITIAL
                                                                       THEN | { dash } | ) }{ payment_term_text-bf_pay_term_text }| ).

  er_entity-incoterms =  condense( |{ sales-inco1 }{ COND #( WHEN sales-inco1 IS NOT INITIAL AND
                                                                  sales-inco2 IS NOT INITIAL
                                                             THEN | { dash } | ) }{ sales-inco2 }| ).

  "  Populating Bill From Details
  " Line 1: Company Name & Address
  er_entity-bfaddress = condense( |{ bf_address-name1 }{ COND #( WHEN bf_address-name1 IS NOT INITIAL
                                                                 THEN | | ) }{ bf_address-name2 }| ).
  " Line 2: Street
  er_entity-bfaddress = condense( COND #( WHEN bf_address-streetname IS NOT INITIAL AND er_entity-bfaddress IS NOT INITIAL
                                          THEN |{ er_entity-bfaddress }{ cl_abap_char_utilities=>newline }{ bf_address-streetname }|
                                          WHEN bf_address-streetname IS NOT INITIAL
                                          THEN |{ bf_address-streetname }|
                                          WHEN er_entity-bfaddress IS NOT INITIAL
                                          THEN |{ er_entity-bfaddress }| ) ).

  DATA(bf_line3) = condense( |{ bf_address-city1 }{ COND #( WHEN bf_address-city1 IS NOT INITIAL AND ( bf_address-post_code1 IS NOT INITIAL OR bf_address-regionname IS NOT INITIAL )
                                                            THEN | { dash } | ) }{ bf_address-post_code1
                                                                 }{ COND #( WHEN bf_address-post_code1 IS NOT INITIAL AND bf_address-regionname IS NOT INITIAL
                                                            THEN |{ coma } | )
                                                                 }{ bf_address-regionname }|  ).
  " Line 3: City, Region & Postal Code
  er_entity-bfaddress = condense( COND #( WHEN bf_line3 IS NOT INITIAL AND er_entity-bfaddress IS NOT INITIAL
                                          THEN |{ er_entity-bfaddress }{ cl_abap_char_utilities=>newline }{ bf_line3 }|
                                          WHEN bf_line3 IS NOT INITIAL
                                          THEN |{ bf_line3 }|
                                          WHEN er_entity-bfaddress IS NOT INITIAL
                                          THEN |{ er_entity-bfaddress }| ) ).

  er_entity-bfstatecode = condense( |{ bf_address-region }{ COND #( WHEN bf_address-region IS NOT INITIAL AND bf_address-regionname IS NOT INITIAL
                                                                    THEN | { dash } | ) }{ bf_address-regionname }| ).
  er_entity-bfplant     = sales-bf_plant.
  er_entity-bfgstin     = bf_gstin-bf_gstin.
  er_entity-bfpan       = VALUE #( bf_iec_pan[ type = TEXT-001 ]-pan OPTIONAL ).
  er_entity-bfiec       = VALUE #( bf_iec_pan[ type = TEXT-002 ]-pan OPTIONAL ).

  """"
  er_entity-sfaddress    = er_entity-bfaddress.
  DATA(sf_line3)         = bf_line3 .
  er_entity-sfaddress    = er_entity-bfaddress .
  er_entity-sfstatecode  =  er_entity-bfstatecode .
  er_entity-sfplant      =  er_entity-bfplant .
  er_entity-sfgstin      =  er_entity-bfgstin .
  er_entity-sfpan        =  er_entity-bfpan   .
  er_entity-sfiec        =   er_entity-bfiec   .
  """"

  "  Populating Bill To Details
  " Line 1: Customer Name & Address
  er_entity-btaddress = condense( |{ bt_address-name1 }{ COND #( WHEN bt_address-name1 IS NOT INITIAL
                                                                 THEN | | ) }{ bt_address-name2 }| ).
  " Line 2: Street
  er_entity-btaddress = condense( COND #( WHEN bt_address-streetname IS NOT INITIAL AND er_entity-btaddress IS NOT INITIAL
                                          THEN |{ er_entity-btaddress }{ cl_abap_char_utilities=>newline }{ bt_address-streetname }|
                                          WHEN bt_address-streetname IS NOT INITIAL
                                          THEN |{ bt_address-streetname }|
                                          WHEN er_entity-btaddress IS NOT INITIAL
                                          THEN |{ er_entity-btaddress }| ) ).

  DATA(bt_line3) = condense( |{ bt_address-city1 }{ COND #( WHEN bt_address-city1 IS NOT INITIAL AND ( bt_address-post_code1 IS NOT INITIAL OR bt_address-regionname IS NOT INITIAL )
                                                            THEN | { dash } | ) }{ bt_address-post_code1
                                                                 }{ COND #( WHEN bt_address-post_code1 IS NOT INITIAL AND bt_address-regionname IS NOT INITIAL
                                                            THEN |{ coma } | )
                                                                 }{ bt_address-regionname }|  ).
  " Line 3: City, Region & Postal Code
  er_entity-btaddress = condense( COND #( WHEN bt_line3 IS NOT INITIAL AND er_entity-btaddress IS NOT INITIAL
                                          THEN |{ er_entity-btaddress }{ cl_abap_char_utilities=>newline }{ bt_line3 }|
                                          WHEN bt_line3 IS NOT INITIAL
                                          THEN |{ bt_line3 }|
                                          WHEN er_entity-btaddress IS NOT INITIAL
                                          THEN |{ er_entity-btaddress }| ) ).

  er_entity-btstatecode = condense( |{ bt_address-region }{ COND #( WHEN bt_address-region IS NOT INITIAL AND bt_address-regionname IS NOT INITIAL
                                                                    THEN | { dash } | ) }{ bt_address-regionname }| ).

  er_entity-btcustomercode = sales-customer.
  er_entity-btgstin        = bt_tax_number-bptaxnumber.
  er_entity-btpan          = bt_address-bt_pan.



  "  Populating Ship From Details
  " Line 1: Company Name & Address
***  er_entity-sfaddress = condense( |{ sf_address-name1 }{ COND #( WHEN sf_address-name1 IS NOT INITIAL
***                                                                 THEN | | ) }{ sf_address-name2 }| ).
***  " Line 2: Street
***  er_entity-sfaddress = condense( COND #( WHEN sf_address-streetname IS NOT INITIAL AND er_entity-sfaddress IS NOT INITIAL
***                                          THEN |{ er_entity-sfaddress }{ cl_abap_char_utilities=>newline }{ sf_address-streetname }|
***                                          WHEN sf_address-streetname IS NOT INITIAL
***                                          THEN |{ sf_address-streetname }|
***                                          WHEN er_entity-sfaddress IS NOT INITIAL
***                                          THEN |{ er_entity-sfaddress }| ) ).

***  DATA(sf_line3) = condense( |{ sf_address-city1 }{ COND #( WHEN sf_address-city1 IS NOT INITIAL AND ( sf_address-post_code1 IS NOT INITIAL OR sf_address-regionname IS NOT INITIAL )
***                                                            THEN | { dash } | ) }{ sf_address-post_code1
***                                                                 }{ COND #( WHEN sf_address-post_code1 IS NOT INITIAL AND sf_address-regionname IS NOT INITIAL
***                                                            THEN |{ coma } | )
***                                                                 }{ sf_address-regionname }|  ).
***  " Line 3: City, Region & Postal Code
***  er_entity-sfaddress = condense( COND #( WHEN sf_line3 IS NOT INITIAL AND er_entity-sfaddress IS NOT INITIAL
***                                          THEN |{ er_entity-sfaddress }{ cl_abap_char_utilities=>newline }{ sf_line3 }|
***                                          WHEN sf_line3 IS NOT INITIAL
***                                          THEN |{ sf_line3 }|
***                                          WHEN er_entity-sfaddress IS NOT INITIAL
***                                          THEN |{ er_entity-sfaddress }| ) ).
***
***  er_entity-sfstatecode = condense( |{ sf_address-region }{ COND #( WHEN sf_address-region IS NOT INITIAL AND sf_address-regionname IS NOT INITIAL
***                                                                    THEN | { dash } | ) }{ sf_address-regionname }| ).
***
***  er_entity-sfpan   = VALUE #( bf_iec_pan[ type = TEXT-001 ]-pan OPTIONAL ).
***  er_entity-sfiec   = VALUE #( bf_iec_pan[ type = TEXT-002 ]-pan OPTIONAL ).
***  er_entity-sfplant = sf_address-sf_plant.
***  er_entity-sfgstin = sf_tax_number-bptaxnumber.


  "  Populating Ship To Details
  " Line 1: Ship To Name & Address
  er_entity-staddress = condense( |{ st_address-name1 }{ COND #( WHEN st_address-name1 IS NOT INITIAL
                                                                 THEN | | ) }{ st_address-name2 }| ).
  " Line 2: Street
  er_entity-staddress = condense( COND #( WHEN st_address-streetname IS NOT INITIAL AND er_entity-staddress IS NOT INITIAL
                                          THEN |{ er_entity-staddress }{ cl_abap_char_utilities=>newline }{ st_address-streetname }|
                                          WHEN st_address-streetname IS NOT INITIAL
                                          THEN |{ st_address-streetname }|
                                          WHEN er_entity-staddress IS NOT INITIAL
                                          THEN |{ er_entity-staddress }| ) ).

  DATA(st_line3) = condense( |{ st_address-city1 }{ COND #( WHEN st_address-city1 IS NOT INITIAL AND ( st_address-post_code1 IS NOT INITIAL OR st_address-regionname IS NOT INITIAL )
                                                            THEN | { dash } | ) }{ st_address-post_code1
                                                                 }{ COND #( WHEN st_address-post_code1 IS NOT INITIAL AND st_address-regionname IS NOT INITIAL
                                                            THEN |{ coma } | )
                                                                 }{ st_address-regionname }|  ).
  " Line 3: City, Region & Postal Code
  er_entity-staddress = condense( COND #( WHEN st_line3 IS NOT INITIAL AND er_entity-staddress IS NOT INITIAL
                                          THEN |{ er_entity-staddress }{ cl_abap_char_utilities=>newline }{ st_line3 }|
                                          WHEN st_line3 IS NOT INITIAL
                                          THEN |{ st_line3 }|
                                          WHEN er_entity-staddress IS NOT INITIAL
                                          THEN |{ er_entity-staddress }| ) ).

  er_entity-ststatecode = condense( |{ st_address-region }{ COND #( WHEN st_address-region IS NOT INITIAL AND st_address-regionname IS NOT INITIAL
                                                                    THEN | { dash } | ) }{ st_address-regionname }| ).
  er_entity-stcustomercode = sales-ship_to_customer.
  er_entity-stgstin        = st_tax_number-bptaxnumber.
  er_entity-stpan          = st_address-st_pan.


  "  Populating Account Details
  er_entity-beneficiaryname    = sales-beneficiary_name.
  er_entity-beneficiaryaddress = er_entity-bfaddress.
  er_entity-bankname           = bank_details-bank_name.
  er_entity-branchaddress      = bank_details-bank_address.
  er_entity-beneficiaryaccno   = bank_details-account_no.
  er_entity-accounttype        = bank_details-account_type_desc.
  er_entity-ifsccode           = bank_details-ifsc_code.

  "  Populating Remark Field
  er_entity-remarks           = remarks.

ENDMETHOD.


  METHOD items_get_entityset.
*----------------------------------------------------------------------*
* Report  ZCL_ZSALES_ORDER_CONFO_DPC_EXT                               *
*----------------------------------------------------------------------*
* Title:          Sales Order Conformation                             *
* RICEF#:         SD 09                                                *
* Transaction:    VA02                                                 *
*----------------------------------------------------------------------*
* Copyright:      NDBS, Inc.                                           *
* Client:         RSB Transmission                                     *
*----------------------------------------------------------------------*
* Developer:      Asafwar Shivam                                       *
* Creation Date:  30-Jul-2025                                          *
*----------------------------------------------------------------------*

    "   data Declaration For Purchase Order.
    DATA(sales_order) = VALUE #( it_key_tab[ name = sales_order ]-value OPTIONAL ).
    DATA : sales_order_no TYPE vbeln.

    "  Fetching Sales Item Details When There Is No Discount Values
    sales_order_no = sales_order.
    me->get_items(
      EXPORTING
        sales_order            =    sales_order_no          " Sales and Distribution Document Number
      IMPORTING
        items_with_discount =  et_entityset   ).            " Used When Discount Value Is Null

*
*    DATA: sales             TYPE STANDARD TABLE OF  zcl_zsales_order_confo_mpc_ext=>ts_so_item WITH EMPTY KEY,
*          sales_detail      TYPE zcl_zsales_order_confo_mpc_ext=>ts_so_item,
*          total_basic_value TYPE kbetr,
*          total_discount    TYPE kbetr,
*          total_amount      TYPE pc207-betrg,
*          amount_in_words   TYPE char256,
*          index             TYPE i,
*          gst_percentage    TYPE i.
*
*
*    "  Fetching Items Details
*    SELECT
*      sales_item~salesdocument,
*      sales_item~salesdocumentitem,
*      sales_item~materialbycustomer                   AS customer_material_code,
*      sales_item~material                             AS rsb_material_code,
*      customer_material~materialdescriptionbycustomer AS customer_material_description,
*      product_text~productname                        AS rsb_material_description,
*      plant_material~consumptiontaxctrlcode           AS hsn_sac_code,
*      control_code_text~consumptiontaxctrlcodetext1   AS hsn_sac_description,
*      sales_item~orderquantity                        AS quantity,
*      sales_item~orderquantityunit                    AS uom,
*      unit_price~conditionratevalue                   AS unit_price,
*      unit_price~conditiontype,
*      sales_header~totalnetamount
*    FROM i_salesdocumentbasic                         AS sales_header
*    LEFT OUTER JOIN i_salesdocumentitembasic AS sales_item
*                                             ON sales_item~salesdocument = sales_header~salesdocument
*    LEFT OUTER JOIN i_customermaterial_2 AS customer_material
*                                         ON customer_material~salesorganization = sales_header~salesorganization
*                                        AND customer_material~distributionchannel = sales_header~distributionchannel
*                                        AND customer_material~customer = sales_header~soldtoparty
*                                        AND customer_material~product = sales_item~material
*    LEFT OUTER JOIN i_producttext AS product_text
*                                  ON product_text~product = sales_item~material
*                                 AND product_text~language = @sy-langu
*    LEFT OUTER JOIN i_productplantbasic AS plant_material
*                                        ON plant_material~product = sales_item~material
*                                       AND plant_material~plant = sales_item~plant
*    LEFT OUTER JOIN i_ae_cnsmpntaxctrlcodetxt AS control_code_text
*                                              ON control_code_text~consumptiontaxctrlcode = plant_material~consumptiontaxctrlcode
*    LEFT OUTER JOIN i_acmpricingelement  AS unit_price
*                                         ON unit_price~pricingdocument = sales_item~salesdocumentcondition
*                                        AND unit_price~pricingdocumentitem = sales_item~salesdocumentitem
*                                        AND unit_price~conditiontype IN ( @text-006, @text-007, @text-008, @text-009, @text-010, @text-011,
*                                                                          @text-012, @text-013, @text-014, @text-015,
*                                                                          @text-016, @text-017, @text-018  )   "'ZPR0' and ZBLK and ZCDS and ZBD1 and ZADC and ZFLD
*    WHERE sales_header~salesdocument = @sales_order
*    INTO TABLE @DATA(sales_details).
*
*    IF sy-subrc = 0.
*      "  Eleminating Duplicate Items From Table
*      DATA(sales_data_unic) = sales_details[].
*      SORT sales_data_unic ASCENDING BY  salesdocumentitem.
*      DELETE ADJACENT DUPLICATES FROM  sales_data_unic COMPARING salesdocumentitem.
*    ENDIF.
*
*    "  Populating Data
*    IF sales_data_unic IS NOT INITIAL.
*      CLEAR : index.
*      LOOP AT sales_data_unic ASSIGNING FIELD-SYMBOL(<sales_deta>).
*        "  Serial Numbering For Line Item
*        index = index + 1.
*        CLEAR : gst_percentage.
*
*        sales_detail-salesorder           = <sales_deta>-salesdocument.
*        sales_detail-itemno               = index.
*        sales_detail-rsbmaterialcode      = <sales_deta>-rsb_material_code.
*        sales_detail-rsbmaterialdesc      = <sales_deta>-rsb_material_description.
*        sales_detail-customermaterialcode = <sales_deta>-customer_material_code.
*        sales_detail-customermaterialdesc = <sales_deta>-customer_material_description.
*        sales_detail-hsnsac               = <sales_deta>-hsn_sac_code.
*        sales_detail-hsnsacdesc           = <sales_deta>-hsn_sac_description.
*        sales_detail-quantity             = <sales_deta>-quantity.
*        sales_detail-uom                  = <sales_deta>-uom.
*        sales_detail-unitprice            = VALUE #( sales_details[ conditiontype = TEXT-006 salesdocumentitem = <sales_deta>-salesdocumentitem ]-unit_price OPTIONAL ).
*        sales_detail-discountpercent      = REDUCE #( INIT discount_percent TYPE kbetr
*                                                      FOR <sales> IN sales_details
*                                                      WHERE ( salesdocumentitem = <sales_deta>-salesdocumentitem AND
*                                                            ( conditiontype = TEXT-007 OR
*                                                              conditiontype = TEXT-008 OR
*                                                              conditiontype = TEXT-009 OR
*                                                              conditiontype = TEXT-010 OR
*                                                              conditiontype = TEXT-011 ) )
*                                                       NEXT discount_percent = discount_percent + <sales>-unit_price ).
*
*        "  Converting negative Value Into Positive
*        IF sales_detail-discountpercent < 0.
*          sales_detail-discountpercent = -1 * sales_detail-discountpercent.
*        ENDIF.
*
*        sales_detail-discountvalue       = sales_detail-discountpercent * <sales_deta>-quantity * sales_detail-unitprice / 100.
*        sales_detail-valueafterdiscount  = ( <sales_deta>-quantity * <sales_deta>-unit_price ) - sales_detail-discountvalue.
*
*        "  Concatinating Discount Percent Symbol To Discount Percentage Value
*        gst_percentage =  sales_detail-discountpercent.
*        sales_detail-discountpercent = |{ gst_percentage }{ percentage }|.
*
*        "  Total Amount
*        total_basic_value = total_basic_value + ( <sales_deta>-quantity * <sales_deta>-unit_price ).
*
*        "  Total Discount
*        total_discount = total_discount + sales_detail-discountvalue.
*
*        APPEND sales_detail TO et_entityset.
*        CLEAR : sales_detail.
*
*      ENDLOOP.
*
*    ENDIF.
*
*    "  Total Basic Value
*    sales_detail-totalbasicvalue = total_basic_value.
*
*    "  Total Discount Value
*    sales_detail-totaldiscount   = total_discount.
*
*    "  Total Value After Discount
*    sales_detail-totalvalueafterdiscount = total_basic_value - total_discount.
*
*    sales_detail-freightcharges = REDUCE #( INIT freight TYPE kbetr
*                                                 FOR <sales> IN sales_details
*                                                 NEXT freight = COND #( WHEN <sales>-conditiontype = TEXT-012           " ZFRE
*                                                                        THEN freight + <sales>-unit_price ) ).
*
*    sales_detail-packagingcharges = REDUCE #( INIT packaging TYPE kbetr
*                                               FOR <sales> IN sales_details
*                                              NEXT packaging = COND #( WHEN <sales>-conditiontype = TEXT-013            " ZPAC
*                                                                          THEN packaging + <sales>-unit_price ) ).
*
*    sales_detail-toolarmotization  = REDUCE #( INIT amortization TYPE kbetr
*                                                FOR <sales> IN sales_details
*                                               NEXT amortization = COND #( WHEN <sales>-conditiontype = TEXT-014         " ZTAC
*                                                                             THEN amortization + <sales>-unit_price ) ).
*
*    "  Taxable Value
*    sales_detail-taxablevalue = VALUE #( sales_details[ salesdocument = sales_order ]-totalnetamount OPTIONAL ).
*
*    CLEAR : gst_percentage.
*    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-015  ]-unit_price OPTIONAL ).            " " JOIG
*    sales_detail-igstpercent = gst_percentage.
*    sales_detail-igstvalue   = sales_detail-taxablevalue * sales_detail-igstpercent / 100.
*
*    CLEAR : gst_percentage.
*    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-016  ]-unit_price OPTIONAL ).            " " JOSG
*    sales_detail-sgstpercent = gst_percentage.
*    sales_detail-sgstvalue   = sales_detail-taxablevalue * sales_detail-sgstpercent / 100.
*
*    CLEAR : gst_percentage.
*    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-017  ]-unit_price OPTIONAL ).            " " JOCG
*    sales_detail-cgstpercent = gst_percentage.
*    sales_detail-cgstvalue   = sales_detail-taxablevalue * sales_detail-sgstpercent / 100.
*
*    CLEAR : gst_percentage.
*    gst_percentage          = VALUE #( sales_details[ conditiontype = TEXT-018  ]-unit_price OPTIONAL ).            " " JTC2
*    sales_detail-tcspercent = gst_percentage.                                                              " " JTC2
*    sales_detail-tcsvalue   = sales_detail-taxablevalue * sales_detail-tcspercent / 100.
*
*    "  Total Gst Value
*    DATA(total_gst_value) = sales_detail-igstvalue + sales_detail-sgstvalue + sales_detail-cgstvalue + sales_detail-tcsvalue.
*
*    "  Total Invoice Value
*    sales_detail-totalinvoicevalue = sales_detail-taxablevalue + total_gst_value.
*
*
*
*    "   Total Invoice Amount to words.
*    CLEAR : total_amount.
*    total_amount = sales_detail-totalinvoicevalue.
*
*    yglobalutility=>amountinwords(
*      EXPORTING
*        iv_amt_in_num   = total_amount
*      IMPORTING
*        ev_amt_in_words = amount_in_words ).
*
*    "  converting amount words to camel naming convection
*    IF sy-subrc = 0.
*      CONDENSE amount_in_words.
*      IF amount_in_words IS NOT INITIAL.
*        TRANSLATE amount_in_words+1(199) TO LOWER CASE.
*        amount_in_words = |{ amount_in_words } { only }|.
*        sales_detail-totalinvoiceinwords = amount_in_words.
*        sales_detail-totalinvoiceinwords = cl_hrpayus_format_string=>conv_first_chars_to_upper_case( sales_detail-totalinvoiceinwords ).
*      ENDIF.
*    ENDIF.
*
*
*    "   Total Gst Amount to words.
*    CLEAR : total_amount.
*    total_amount = total_gst_value.
*
*    yglobalutility=>amountinwords(
*      EXPORTING
*        iv_amt_in_num   = total_amount
*      IMPORTING
*        ev_amt_in_words = amount_in_words ).
*
*    "  converting amount words to camel naming convection
*    IF sy-subrc = 0.
*      CONDENSE amount_in_words.
*      IF amount_in_words IS NOT INITIAL.
*        TRANSLATE amount_in_words+1(199) TO LOWER CASE.
*        amount_in_words = |{ amount_in_words } { only }|.
*        sales_detail-totalgstinwords = amount_in_words.
*        sales_detail-totalgstinwords = cl_hrpayus_format_string=>conv_first_chars_to_upper_case( sales_detail-totalgstinwords ).
*      ENDIF.
*    ENDIF.
*
*
**    "  Populating  Amount Details
*    MODIFY et_entityset FROM VALUE #( totalbasicvalue          = sales_detail-totalbasicvalue
*                                      totaldiscount            = sales_detail-totaldiscount
*                                      totalvalueafterdiscount  = sales_detail-totalvalueafterdiscount
*                                      freightcharges           = sales_detail-freightcharges
*                                      packagingcharges         = sales_detail-packagingcharges
*                                      toolarmotization         = sales_detail-toolarmotization
*                                      taxablevalue             = sales_detail-taxablevalue
*                                      igstpercent              = sales_detail-igstpercent
*                                      igstvalue                = sales_detail-igstvalue
*                                      sgstpercent              = sales_detail-sgstpercent
*                                      sgstvalue                = sales_detail-sgstvalue
*                                      cgstpercent              = sales_detail-cgstpercent
*                                      cgstvalue                = sales_detail-cgstvalue
*                                      tcspercent               = sales_detail-tcspercent
*                                      tcsvalue                 = sales_detail-tcsvalue
*                                      lesstaxablevalue         = sales_detail-lesstaxablevalue
*                                      totalinvoicevalue        = sales_detail-totalinvoicevalue
*                                      totalinvoiceinwords      = sales_detail-totalinvoiceinwords
*                                      totalgstinwords          = sales_detail-totalgstinwords )
*                        TRANSPORTING  totalbasicvalue
*                                      totaldiscount
*                                      totalvalueafterdiscount
*                                      freightcharges
*                                      packagingcharges
*                                      toolarmotization
*                                      taxablevalue
*                                      igstpercent
*                                      igstvalue
*                                      sgstpercent
*                                      sgstvalue
*                                      cgstpercent
*                                      cgstvalue
*                                      tcspercent
*                                      tcsvalue
*                                      lesstaxablevalue
*                                      totalinvoicevalue
*                                      totalinvoiceinwords
*                                      totalgstinwords
*                                      totalgstinwords
*                                      totalinvoiceinwords
*                        WHERE itemno IS NOT INITIAL.

  ENDMETHOD.


  METHOD get_items.

    "  Data Declaration
    DATA: sales             TYPE STANDARD TABLE OF  zcl_zsales_order_confo_mpc_ext=>ts_so_item WITH EMPTY KEY,
          sales_detail      TYPE zcl_zsales_order_confo_mpc_ext=>ts_so_item,
          sales_no_dicount  TYPE zcl_zsales_order_confo_mpc_ext=>ts_so_item_without_discount,
          total_basic_value TYPE kbetr,
          total_discount    TYPE kbetr,
          total_amount      TYPE pc207-betrg,
          amount_in_words   TYPE char256,
          index             TYPE i,
          gst_percentage    TYPE i,
          discount_flag     TYPE char5,
          total_gst_value   TYPE netwr.

    "  Fetching Items Details
    SELECT
      sales_item~salesdocument,
      sales_item~salesdocumentitem,
      sales_item~materialbycustomer                   AS customer_material_code,
      sales_item~material                             AS rsb_material_code,
      customer_material~materialdescriptionbycustomer AS customer_material_description,
      product_text~productname                        AS rsb_material_description,
      plant_material~consumptiontaxctrlcode           AS hsn_sac_code,
      control_code_text~consumptiontaxctrlcodetext1   AS hsn_sac_description,
      sales_item~orderquantity                        AS quantity,
      sales_item~orderquantityunit                    AS uom,
      unit_price~conditionratevalue                   AS unit_price,
      unit_price~conditiontype,
      sales_header~totalnetamount
    FROM i_salesdocumentbasic                         AS sales_header
    LEFT OUTER JOIN i_salesdocumentitembasic AS sales_item
                                             ON sales_item~salesdocument = sales_header~salesdocument
    LEFT OUTER JOIN i_customermaterial_2 AS customer_material
                                         ON customer_material~salesorganization = sales_header~salesorganization
                                        AND customer_material~distributionchannel = sales_header~distributionchannel
                                        AND customer_material~customer = sales_header~soldtoparty
                                        AND customer_material~product = sales_item~material
    LEFT OUTER JOIN i_producttext AS product_text
                                  ON product_text~product = sales_item~material
                                 AND product_text~language = @sy-langu
    LEFT OUTER JOIN i_productplantbasic AS plant_material
                                        ON plant_material~product = sales_item~material
                                       AND plant_material~plant = sales_item~plant
    LEFT OUTER JOIN i_ae_cnsmpntaxctrlcodetxt AS control_code_text
                                              ON control_code_text~consumptiontaxctrlcode = plant_material~consumptiontaxctrlcode
    LEFT OUTER JOIN i_acmpricingelement  AS unit_price
                                         ON unit_price~pricingdocument = sales_item~salesdocumentcondition
                                        AND unit_price~pricingdocumentitem = sales_item~salesdocumentitem
                                        AND unit_price~conditiontype IN ( @text-006, @text-007, @text-008, @text-009, @text-010, @text-011,
                                                                          @text-012, @text-013, @text-014, @text-015,
                                                                          @text-016, @text-017, @text-018  )   "'ZPR0' and ZBLK and ZCDS and ZBD1 and ZADC and ZFLD
    WHERE sales_header~salesdocument = @sales_order
    INTO TABLE @DATA(sales_details).

    IF sy-subrc = 0.
      "  Eleminating Duplicate Items From Table
      DATA(sales_data_unic) = sales_details[].
      SORT sales_data_unic ASCENDING BY  salesdocumentitem.
      DELETE ADJACENT DUPLICATES FROM  sales_data_unic COMPARING salesdocumentitem.
    ENDIF.

    "  Populating Data
    IF sales_data_unic IS NOT INITIAL.
      CLEAR : index.
      discount_flag = 'False'.

      LOOP AT sales_data_unic ASSIGNING FIELD-SYMBOL(<sales_deta>).
        "  Serial Numbering For Line Item
        index = index + 1.
        CLEAR : gst_percentage.

        sales_detail-salesorder           = <sales_deta>-salesdocument.
        sales_detail-itemno               = index.
        sales_detail-rsbmaterialcode      = |{ <sales_deta>-rsb_material_code ALPHA = OUT }|.
        sales_detail-rsbmaterialdesc      = <sales_deta>-rsb_material_description.
        sales_detail-customermaterialcode = <sales_deta>-customer_material_code.
        sales_detail-customermaterialdesc = <sales_deta>-customer_material_description.
        sales_detail-hsnsac               = <sales_deta>-hsn_sac_code.
        sales_detail-hsnsacdesc           = <sales_deta>-hsn_sac_description.
        sales_detail-quantity             = <sales_deta>-quantity.

        " Start of changes by Vaishnavi Vairagare on 07/01/2025
        IF <sales_deta>-uom IS NOT INITIAL.
          CALL FUNCTION 'CONVERSION_EXIT_CUNIT_OUTPUT'
            EXPORTING
              input          = <sales_deta>-uom
              language       = sy-langu
            IMPORTING
*             LONG_TEXT      =
              output         = <sales_deta>-uom
*             SHORT_TEXT     =
            EXCEPTIONS
              unit_not_found = 1
              OTHERS         = 2.
          IF sy-subrc <> 0.
* Implement suitable error handling here
          ENDIF.
        ENDIF.
        sales_detail-uom                  = <sales_deta>-uom.

        " End of changes by Vaishnavi Vairagare on 07/01/2025
        sales_detail-unitprice            = VALUE #( sales_details[ conditiontype = TEXT-006 salesdocumentitem = <sales_deta>-salesdocumentitem ]-unit_price OPTIONAL ).
        sales_detail-discountpercent      = REDUCE #( INIT discount_percent TYPE kbetr
                                                      FOR <sales> IN sales_details
                                                      WHERE ( salesdocumentitem = <sales_deta>-salesdocumentitem AND
                                                            ( conditiontype = TEXT-007 OR
                                                              conditiontype = TEXT-008 OR
                                                              conditiontype = TEXT-009 OR
                                                              conditiontype = TEXT-010 OR
                                                              conditiontype = TEXT-011 ) )
                                                       NEXT discount_percent = discount_percent + <sales>-unit_price ).

        "  Converting negative Value Into Positive
        IF sales_detail-discountpercent < 0.
          sales_detail-discountpercent = -1 * sales_detail-discountpercent.
        ENDIF.

        "  If Discount Percentage Is Not Initial Then Initializing Discount Flag
        IF sales_detail-discountpercent > 0.
          discount_flag = 'True'.
        ENDIF.

        sales_detail-discountvalue       = sales_detail-discountpercent * <sales_deta>-quantity * sales_detail-unitprice / 100.
        sales_detail-valueafterdiscount  = ( <sales_deta>-quantity * sales_detail-unitprice  ) - sales_detail-discountvalue.

        "  Concatinating Discount Percent Symbol To Discount Percentage Value
        gst_percentage =  sales_detail-discountpercent.
        sales_detail-discountpercent = |{ gst_percentage }{ percentage }|.

        "  Total Amount
        total_basic_value = total_basic_value + ( <sales_deta>-quantity * sales_detail-unitprice  ).

        "  Total Discount
        total_discount = total_discount + sales_detail-discountvalue.


        sales_no_dicount = CORRESPONDING #( sales_detail ).
        APPEND sales_detail TO items_with_discount.
        APPEND sales_no_dicount TO items_without_discount.
        CLEAR : sales_detail, sales_no_dicount.

      ENDLOOP.

    ENDIF.

    "  Total Basic Value
    sales_detail-totalbasicvalue = total_basic_value.

    "  Total Discount Value
    sales_detail-totaldiscount   = total_discount.

    "  Total Value After Discount
    sales_detail-totalvalueafterdiscount = total_basic_value - total_discount.

    sales_detail-freightcharges = REDUCE #( INIT freight TYPE kbetr
                                                 FOR <sales> IN sales_details
                                                 NEXT freight = COND #( WHEN <sales>-conditiontype = TEXT-012           " ZFRE
                                                                        THEN freight + <sales>-unit_price ) ).

    sales_detail-packagingcharges = REDUCE #( INIT packaging TYPE kbetr
                                               FOR <sales> IN sales_details
                                              NEXT packaging = COND #( WHEN <sales>-conditiontype = TEXT-013            " ZPAC
                                                                          THEN packaging + <sales>-unit_price ) ).

    sales_detail-toolarmotization  = REDUCE #( INIT amortization TYPE kbetr
                                                FOR <sales> IN sales_details
                                               NEXT amortization = COND #( WHEN <sales>-conditiontype = TEXT-014         " ZTAC
                                                                             THEN amortization + <sales>-unit_price ) ).

    "  Taxable Value
    sales_detail-taxablevalue = VALUE #( sales_details[ salesdocument = sales_order ]-totalnetamount OPTIONAL ).

    CLEAR : gst_percentage.
    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-015  ]-unit_price OPTIONAL ).            " " JOIG
    sales_detail-igstpercent = gst_percentage.
    sales_detail-igstvalue   = sales_detail-taxablevalue * sales_detail-igstpercent / 100.

    CLEAR : gst_percentage.
    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-016  ]-unit_price OPTIONAL ).            " " JOSG
    sales_detail-sgstpercent = gst_percentage.
    sales_detail-sgstvalue   = sales_detail-taxablevalue * sales_detail-sgstpercent / 100.

    CLEAR : gst_percentage.
    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-017  ]-unit_price OPTIONAL ).            " " JOCG
    sales_detail-cgstpercent = gst_percentage.
    sales_detail-cgstvalue   = sales_detail-taxablevalue * sales_detail-sgstpercent / 100.

    CLEAR : gst_percentage.
    gst_percentage          = VALUE #( sales_details[ conditiontype = TEXT-018  ]-unit_price OPTIONAL ).            " " JTC2
    sales_detail-tcspercent = gst_percentage.                                                              " " JTC2
    sales_detail-tcsvalue   = sales_detail-taxablevalue * sales_detail-tcspercent / 100.

    "  Total Gst Value
    total_gst_value = sales_detail-igstvalue + sales_detail-sgstvalue + sales_detail-cgstvalue + sales_detail-tcsvalue.

    "  Total Invoice Value
    sales_detail-totalinvoicevalue = sales_detail-taxablevalue + total_gst_value.



    "   Total Invoice Amount to words.
    CLEAR : total_amount.
    total_amount = sales_detail-totalinvoicevalue.

    yglobalutility=>amountinwords(
      EXPORTING
        iv_amt_in_num   = total_amount
      IMPORTING
        ev_amt_in_words = amount_in_words ).

    "  converting amount words to camel naming convection
    IF sy-subrc = 0.
      CONDENSE amount_in_words.
      IF amount_in_words IS NOT INITIAL.
        TRANSLATE amount_in_words+1(199) TO LOWER CASE.
        amount_in_words = |{ amount_in_words } { only }|.
        sales_detail-totalinvoiceinwords = amount_in_words.
        sales_detail-totalinvoiceinwords = cl_hrpayus_format_string=>conv_first_chars_to_upper_case( sales_detail-totalinvoiceinwords ).
      ENDIF.
    ENDIF.


    "   Total Gst Amount to words.
    CLEAR : total_amount.
    total_amount = total_gst_value.

    yglobalutility=>amountinwords(
      EXPORTING
        iv_amt_in_num   = total_amount
      IMPORTING
        ev_amt_in_words = amount_in_words ).

    "  converting amount words to camel naming convection
    IF sy-subrc = 0.
      CONDENSE amount_in_words.
      IF amount_in_words IS NOT INITIAL.
        TRANSLATE amount_in_words+1(199) TO LOWER CASE.
        amount_in_words = |{ amount_in_words } { only }|.
        sales_detail-totalgstinwords = amount_in_words.
        sales_detail-totalgstinwords = cl_hrpayus_format_string=>conv_first_chars_to_upper_case( sales_detail-totalgstinwords ).
      ENDIF.
    ENDIF.


*    "  Populating  Amount Details
    MODIFY items_with_discount FROM VALUE #( totalbasicvalue   = sales_detail-totalbasicvalue
                                             totaldiscount            = sales_detail-totaldiscount
                                             totalvalueafterdiscount  = sales_detail-totalvalueafterdiscount
                                             freightcharges           = sales_detail-freightcharges
                                             packagingcharges         = sales_detail-packagingcharges
                                             toolarmotization         = sales_detail-toolarmotization
                                             taxablevalue             = sales_detail-taxablevalue
                                             igstpercent              = sales_detail-igstpercent
                                             igstvalue                = sales_detail-igstvalue
                                             sgstpercent              = sales_detail-sgstpercent
                                             sgstvalue                = sales_detail-sgstvalue
                                             cgstpercent              = sales_detail-cgstpercent
                                             cgstvalue                = sales_detail-cgstvalue
                                             tcspercent               = sales_detail-tcspercent
                                             tcsvalue                 = sales_detail-tcsvalue
                                             lesstaxablevalue         = sales_detail-lesstaxablevalue
                                             totalinvoicevalue        = sales_detail-totalinvoicevalue
                                             totalinvoiceinwords      = sales_detail-totalinvoiceinwords
                                             totalgstinwords          = sales_detail-totalgstinwords
                                             discountflag             = discount_flag )
                               TRANSPORTING  totalbasicvalue
                                             totaldiscount
                                             totalvalueafterdiscount
                                             freightcharges
                                             packagingcharges
                                             toolarmotization
                                             taxablevalue
                                             igstpercent
                                             igstvalue
                                             sgstpercent
                                             sgstvalue
                                             cgstpercent
                                             cgstvalue
                                             tcspercent
                                             tcsvalue
                                             lesstaxablevalue
                                             totalinvoicevalue
                                             totalinvoiceinwords
                                             totalgstinwords
                                             totalgstinwords
                                             totalinvoiceinwords
                                             discountflag
                              WHERE itemno IS NOT INITIAL.

    MODIFY items_without_discount FROM VALUE #( totalbasicvalue   = sales_detail-totalbasicvalue
                                         totaldiscount            = sales_detail-totaldiscount
                                         totalvalueafterdiscount  = sales_detail-totalvalueafterdiscount
                                         freightcharges           = sales_detail-freightcharges
                                         packagingcharges         = sales_detail-packagingcharges
                                         toolarmotization         = sales_detail-toolarmotization
                                         taxablevalue             = sales_detail-taxablevalue
                                         igstpercent              = sales_detail-igstpercent
                                         igstvalue                = sales_detail-igstvalue
                                         sgstpercent              = sales_detail-sgstpercent
                                         sgstvalue                = sales_detail-sgstvalue
                                         cgstpercent              = sales_detail-cgstpercent
                                         cgstvalue                = sales_detail-cgstvalue
                                         tcspercent               = sales_detail-tcspercent
                                         tcsvalue                 = sales_detail-tcsvalue
                                         lesstaxablevalue         = sales_detail-lesstaxablevalue
                                         totalinvoicevalue        = sales_detail-totalinvoicevalue
                                         totalinvoiceinwords      = sales_detail-totalinvoiceinwords
                                         totalgstinwords          = sales_detail-totalgstinwords
                                         discountflag             = discount_flag )
                           TRANSPORTING  totalbasicvalue
                                         totaldiscount
                                         totalvalueafterdiscount
                                         freightcharges
                                         packagingcharges
                                         toolarmotization
                                         taxablevalue
                                         igstpercent
                                         igstvalue
                                         sgstpercent
                                         sgstvalue
                                         cgstpercent
                                         cgstvalue
                                         tcspercent
                                         tcsvalue
                                         lesstaxablevalue
                                         totalinvoicevalue
                                         totalinvoiceinwords
                                         totalgstinwords
                                         totalgstinwords
                                         totalinvoiceinwords
                                         discountflag
                          WHERE itemno IS NOT INITIAL.


  ENDMETHOD.


  METHOD items_no_disc_get_entityset.
*----------------------------------------------------------------------*
* Report  ZCL_ZSALES_ORDER_CONFO_DPC_EXT                               *
*----------------------------------------------------------------------*
* Title:          Sales Order Conformation                             *
* RICEF#:         SD 09                                                *
* Transaction:    VA02                                                 *
*----------------------------------------------------------------------*
* Copyright:      NDBS, Inc.                                           *
* Client:         RSB Transmission                                     *
*----------------------------------------------------------------------*
* Developer:      Asafwar Shivam                                       *
* Creation Date:  30-Jul-2025                                          *
*----------------------------------------------------------------------*

    "   data Declaration For Purchase Order.
    DATA(sales_order) = VALUE #( it_key_tab[ name = sales_order ]-value OPTIONAL ).
    DATA : sales_order_no TYPE vbeln.

    "  Fetching Sales Item Details When There Is No Discount Values
    sales_order_no = sales_order.
    me->get_items(
      EXPORTING
        sales_order            =    sales_order_no          " Sales and Distribution Document Number
      IMPORTING
        items_without_discount =  et_entityset   ).         " Used When Discount Value Is Null

*
*
*
*    DATA: sales             TYPE STANDARD TABLE OF  zcl_zsales_order_confo_mpc_ext=>Ts_SO_ITEM_WITHOUT_DISCOUNT WITH EMPTY KEY,
*          sales_detail      TYPE zcl_zsales_order_confo_mpc_ext=>Ts_SO_ITEM_WITHOUT_DISCOUNT,
*          total_basic_value TYPE kbetr,
*          total_discount    TYPE kbetr,
*          total_amount      TYPE pc207-betrg,
*          amount_in_words   TYPE char256,
*          index             TYPE i,
*          gst_percentage    TYPE i.
*
*
*    "  Fetching Items Details
*    SELECT
*      sales_item~salesdocument,
*      sales_item~salesdocumentitem,
*      sales_item~materialbycustomer                   AS customer_material_code,
*      sales_item~material                             AS rsb_material_code,
*      customer_material~materialdescriptionbycustomer AS customer_material_description,
*      product_text~productname                        AS rsb_material_description,
*      plant_material~consumptiontaxctrlcode           AS hsn_sac_code,
*      control_code_text~consumptiontaxctrlcodetext1   AS hsn_sac_description,
*      sales_item~orderquantity                        AS quantity,
*      sales_item~orderquantityunit                    AS uom,
*      unit_price~conditionratevalue                   AS unit_price,
*      unit_price~conditiontype,
*      sales_header~totalnetamount
*    FROM i_salesdocumentbasic                         AS sales_header
*    LEFT OUTER JOIN i_salesdocumentitembasic AS sales_item
*                                             ON sales_item~salesdocument = sales_header~salesdocument
*    LEFT OUTER JOIN i_customermaterial_2 AS customer_material
*                                         ON customer_material~salesorganization = sales_header~salesorganization
*                                        AND customer_material~distributionchannel = sales_header~distributionchannel
*                                        AND customer_material~customer = sales_header~soldtoparty
*                                        AND customer_material~product = sales_item~material
*    LEFT OUTER JOIN i_producttext AS product_text
*                                  ON product_text~product = sales_item~material
*                                 AND product_text~language = @sy-langu
*    LEFT OUTER JOIN i_productplantbasic AS plant_material
*                                        ON plant_material~product = sales_item~material
*                                       AND plant_material~plant = sales_item~plant
*    LEFT OUTER JOIN i_ae_cnsmpntaxctrlcodetxt AS control_code_text
*                                              ON control_code_text~consumptiontaxctrlcode = plant_material~consumptiontaxctrlcode
*    LEFT OUTER JOIN i_acmpricingelement  AS unit_price
*                                         ON unit_price~pricingdocument = sales_item~salesdocumentcondition
*                                        AND unit_price~pricingdocumentitem = sales_item~salesdocumentitem
*                                        AND unit_price~conditiontype IN ( @text-006, @text-007, @text-008, @text-009, @text-010, @text-011,
*                                                                          @text-012, @text-013, @text-014, @text-015,
*                                                                          @text-016, @text-017, @text-018  )   "'ZPR0' and ZBLK and ZCDS and ZBD1 and ZADC and ZFLD
*    WHERE sales_header~salesdocument = @sales_order
*    INTO TABLE @DATA(sales_details).
*
*    IF sy-subrc = 0.
*      "  Eleminating Duplicate Items From Table
*      DATA(sales_data_unic) = sales_details[].
*      SORT sales_data_unic ASCENDING BY  salesdocumentitem.
*      DELETE ADJACENT DUPLICATES FROM  sales_data_unic COMPARING salesdocumentitem.
*    ENDIF.
*
*    "  Populating Data
*    IF sales_data_unic IS NOT INITIAL.
*      CLEAR : index.
*      LOOP AT sales_data_unic ASSIGNING FIELD-SYMBOL(<sales_deta>).
*        "  Serial Numbering For Line Item
*        index = index + 1.
*        CLEAR : gst_percentage.
*
*        sales_detail-salesorder           = <sales_deta>-salesdocument.
*        sales_detail-itemno               = index.
*        sales_detail-rsbmaterialcode      = <sales_deta>-rsb_material_code.
*        sales_detail-rsbmaterialdesc      = <sales_deta>-rsb_material_description.
*        sales_detail-customermaterialcode = <sales_deta>-customer_material_code.
*        sales_detail-customermaterialdesc = <sales_deta>-customer_material_description.
*        sales_detail-hsnsac               = <sales_deta>-hsn_sac_code.
*        sales_detail-hsnsacdesc           = <sales_deta>-hsn_sac_description.
*        sales_detail-quantity             = <sales_deta>-quantity.
*        sales_detail-uom                  = <sales_deta>-uom.
*        sales_detail-unitprice            = VALUE #( sales_details[ conditiontype = TEXT-006 salesdocumentitem = <sales_deta>-salesdocumentitem ]-unit_price OPTIONAL ).
*        sales_detail-discountpercent      = REDUCE #( INIT discount_percent TYPE kbetr
*                                                      FOR <sales> IN sales_details
*                                                      WHERE ( salesdocumentitem = <sales_deta>-salesdocumentitem AND
*                                                            ( conditiontype = TEXT-007 OR
*                                                              conditiontype = TEXT-008 OR
*                                                              conditiontype = TEXT-009 OR
*                                                              conditiontype = TEXT-010 OR
*                                                              conditiontype = TEXT-011 ) )
*                                                       NEXT discount_percent = discount_percent + <sales>-unit_price ).
*
*        "  Converting negative Value Into Positive
*        IF sales_detail-discountpercent < 0.
*          sales_detail-discountpercent = -1 * sales_detail-discountpercent.
*        ENDIF.
*
*        sales_detail-discountvalue       = sales_detail-discountpercent * <sales_deta>-quantity * sales_detail-unitprice / 100.
*        sales_detail-valueafterdiscount  = ( <sales_deta>-quantity * <sales_deta>-unit_price ) - sales_detail-discountvalue.
*
*        "  Concatinating Discount Percent Symbol To Discount Percentage Value
*        gst_percentage =  sales_detail-discountpercent.
*        sales_detail-discountpercent = |{ gst_percentage }{ percentage }|.
*
*        "  Total Amount
*        total_basic_value = total_basic_value + ( <sales_deta>-quantity * <sales_deta>-unit_price ).
*
*        "  Total Discount
*        total_discount = total_discount + sales_detail-discountvalue.
*
*        APPEND sales_detail TO ET_ENTITYSET.
*        CLEAR : sales_detail.
*
*      ENDLOOP.
*
*    ENDIF.
*
*    "  Total Basic Value
*    sales_detail-totalbasicvalue = total_basic_value.
*
*    "  Total Discount Value
*    sales_detail-totaldiscount   = total_discount.
*
*    "  Total Value After Discount
*    sales_detail-totalvalueafterdiscount = total_basic_value - total_discount.
*
*    sales_detail-freightcharges = REDUCE #( INIT freight TYPE kbetr
*                                                 FOR <sales> IN sales_details
*                                                 NEXT freight = COND #( WHEN <sales>-conditiontype = TEXT-012           " ZFRE
*                                                                        THEN freight + <sales>-unit_price ) ).
*
*    sales_detail-packagingcharges = REDUCE #( INIT packaging TYPE kbetr
*                                               FOR <sales> IN sales_details
*                                              NEXT packaging = COND #( WHEN <sales>-conditiontype = TEXT-013            " ZPAC
*                                                                          THEN packaging + <sales>-unit_price ) ).
*
*    sales_detail-toolarmotization  = REDUCE #( INIT amortization TYPE kbetr
*                                                FOR <sales> IN sales_details
*                                               NEXT amortization = COND #( WHEN <sales>-conditiontype = TEXT-014         " ZTAC
*                                                                             THEN amortization + <sales>-unit_price ) ).
*
*    "  Taxable Value
*    sales_detail-taxablevalue = VALUE #( sales_details[ salesdocument = sales_order ]-totalnetamount OPTIONAL ).
*
*    CLEAR : gst_percentage.
*    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-015  ]-unit_price OPTIONAL ).            " " JOIG
*    sales_detail-igstpercent = gst_percentage.
*    sales_detail-igstvalue   = sales_detail-taxablevalue * sales_detail-igstpercent / 100.
*
*    CLEAR : gst_percentage.
*    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-016  ]-unit_price OPTIONAL ).            " " JOSG
*    sales_detail-sgstpercent = gst_percentage.
*    sales_detail-sgstvalue   = sales_detail-taxablevalue * sales_detail-sgstpercent / 100.
*
*    CLEAR : gst_percentage.
*    gst_percentage           = VALUE #( sales_details[ conditiontype = TEXT-017  ]-unit_price OPTIONAL ).            " " JOCG
*    sales_detail-cgstpercent = gst_percentage.
*    sales_detail-cgstvalue   = sales_detail-taxablevalue * sales_detail-sgstpercent / 100.
*
*    CLEAR : gst_percentage.
*    gst_percentage          = VALUE #( sales_details[ conditiontype = TEXT-018  ]-unit_price OPTIONAL ).            " " JTC2
*    sales_detail-tcspercent = gst_percentage.                                                              " " JTC2
*    sales_detail-tcsvalue   = sales_detail-taxablevalue * sales_detail-tcspercent / 100.
*
*    "  Total Gst Value
*    DATA(total_gst_value) = sales_detail-igstvalue + sales_detail-sgstvalue + sales_detail-cgstvalue + sales_detail-tcsvalue.
*
*    "  Total Invoice Value
*    sales_detail-totalinvoicevalue = sales_detail-taxablevalue + total_gst_value.
*
*
*
*    "   Total Invoice Amount to words.
*    CLEAR : total_amount.
*    total_amount = sales_detail-totalinvoicevalue.
*
*    yglobalutility=>amountinwords(
*      EXPORTING
*        iv_amt_in_num   = total_amount
*      IMPORTING
*        ev_amt_in_words = amount_in_words ).
*
*    "  converting amount words to camel naming convection
*    IF sy-subrc = 0.
*      CONDENSE amount_in_words.
*      IF amount_in_words IS NOT INITIAL.
*        TRANSLATE amount_in_words+1(199) TO LOWER CASE.
*        amount_in_words = |{ amount_in_words } { only }|.
*        sales_detail-totalinvoiceinwords = amount_in_words.
*        sales_detail-totalinvoiceinwords = cl_hrpayus_format_string=>conv_first_chars_to_upper_case( sales_detail-totalinvoiceinwords ).
*      ENDIF.
*    ENDIF.
*
*
*    "   Total Gst Amount to words.
*    CLEAR : total_amount.
*    total_amount = total_gst_value.
*
*    yglobalutility=>amountinwords(
*      EXPORTING
*        iv_amt_in_num   = total_amount
*      IMPORTING
*        ev_amt_in_words = amount_in_words ).
*
*    "  converting amount words to camel naming convection
*    IF sy-subrc = 0.
*      CONDENSE amount_in_words.
*      IF amount_in_words IS NOT INITIAL.
*        TRANSLATE amount_in_words+1(199) TO LOWER CASE.
*        amount_in_words = |{ amount_in_words } { only }|.
*        sales_detail-totalgstinwords = amount_in_words.
*        sales_detail-totalgstinwords = cl_hrpayus_format_string=>conv_first_chars_to_upper_case( sales_detail-totalgstinwords ).
*      ENDIF.
*    ENDIF.
*
*
**    "  Populating  Amount Details
*    MODIFY et_entityset FROM VALUE #( totalbasicvalue          = sales_detail-totalbasicvalue
*                                      totaldiscount            = sales_detail-totaldiscount
*                                      totalvalueafterdiscount  = sales_detail-totalvalueafterdiscount
*                                      freightcharges           = sales_detail-freightcharges
*                                      packagingcharges         = sales_detail-packagingcharges
*                                      toolarmotization         = sales_detail-toolarmotization
*                                      taxablevalue             = sales_detail-taxablevalue
*                                      igstpercent              = sales_detail-igstpercent
*                                      igstvalue                = sales_detail-igstvalue
*                                      sgstpercent              = sales_detail-sgstpercent
*                                      sgstvalue                = sales_detail-sgstvalue
*                                      cgstpercent              = sales_detail-cgstpercent
*                                      cgstvalue                = sales_detail-cgstvalue
*                                      tcspercent               = sales_detail-tcspercent
*                                      tcsvalue                 = sales_detail-tcsvalue
*                                      lesstaxablevalue         = sales_detail-lesstaxablevalue
*                                      totalinvoicevalue        = sales_detail-totalinvoicevalue
*                                      totalinvoiceinwords      = sales_detail-totalinvoiceinwords
*                                      totalgstinwords          = sales_detail-totalgstinwords )
*                        TRANSPORTING  totalbasicvalue
*                                      totaldiscount
*                                      totalvalueafterdiscount
*                                      freightcharges
*                                      packagingcharges
*                                      toolarmotization
*                                      taxablevalue
*                                      igstpercent
*                                      igstvalue
*                                      sgstpercent
*                                      sgstvalue
*                                      cgstpercent
*                                      cgstvalue
*                                      tcspercent
*                                      tcsvalue
*                                      lesstaxablevalue
*                                      totalinvoicevalue
*                                      totalinvoiceinwords
*                                      totalgstinwords
*                                      totalgstinwords
*                                      totalinvoiceinwords
*                        WHERE itemno IS NOT INITIAL.

  ENDMETHOD.

ENDCLASS.
***********************************************************************************************

class ZCL_ZSALES_ORDER_CONFO_MPC definition
  public
  inheriting from /IWBEP/CL_MGW_PUSH_ABS_MODEL
  create public .

public section.

  types:
     TS_ADDITIONALITEMTEXTLINENODE type TDS_SD_SLS_FDP_ITM_TXT_LINE .
  types:
TT_ADDITIONALITEMTEXTLINENODE type standard table of TS_ADDITIONALITEMTEXTLINENODE .
  types:
   begin of ts_text_element,
      artifact_name  type c length 40,       " technical name
      artifact_type  type c length 4,
      parent_artifact_name type c length 40, " technical name
      parent_artifact_type type c length 4,
      text_symbol    type textpoolky,
   end of ts_text_element .
  types:
         tt_text_elements type standard table of ts_text_element with key text_symbol .
  types:
     TS_ADDITIONALITEMTEXTNODE type TDS_SD_SLS_FDP_ITM_TXT .
  types:
TT_ADDITIONALITEMTEXTNODE type standard table of TS_ADDITIONALITEMTEXTNODE .
  types:
     TS_BILLTOPARTYNODE type TDS_SD_SLS_FDP_PARTNER .
  types:
TT_BILLTOPARTYNODE type standard table of TS_BILLTOPARTYNODE .
  types:
     TS_BILLINGMILESTONEITEMNODE type TDS_SD_SLS_FDP_BILLPLAN .
  types:
TT_BILLINGMILESTONEITEMNODE type standard table of TS_BILLINGMILESTONEITEMNODE .
  types:
     TS_BILLINGPERIODICITEMNODE type TDS_SD_SLS_FDP_BILLPLAN .
  types:
TT_BILLINGPERIODICITEMNODE type standard table of TS_BILLINGPERIODICITEMNODE .
  types:
     TS_CHGDOCITMDTLTXTNODE type TDS_SD_SLS_FDP_CHGDOC_TEXT .
  types:
TT_CHGDOCITMDTLTXTNODE type standard table of TS_CHGDOCITMDTLTXTNODE .
  types:
     TS_CHGDOCSUMDTLTXTNODE type TDS_SD_SLS_FDP_CHGDOC_TEXT .
  types:
TT_CHGDOCSUMDTLTXTNODE type standard table of TS_CHGDOCSUMDTLTXTNODE .
  types:
     TS_CHGDOCSUMHDRTXTNODE type TDS_SD_SLS_FDP_CHGDOC_TEXT .
  types:
TT_CHGDOCSUMHDRTXTNODE type standard table of TS_CHGDOCSUMHDRTXTNODE .
  types:
     TS_CHGDOCSUMITMTXTNODE type TDS_SD_SLS_FDP_CHGDOC_TEXT .
  types:
TT_CHGDOCSUMITMTXTNODE type standard table of TS_CHGDOCSUMITMTXTNODE .
  types:
     TS_COMPANYNODE type TDS_SD_SLS_FDP_COMPANY .
  types:
TT_COMPANYNODE type standard table of TS_COMPANYNODE .
  types:
     TS_CONFIGURATIONITEMNODE type TDS_SD_SLS_FDP_ITEM_CONFIG .
  types:
TT_CONFIGURATIONITEMNODE type standard table of TS_CONFIGURATIONITEMNODE .
  types:
     TS_INCOTERMSHEADERNODE type TDS_SD_SLS_FDP_INCOTERMS .
  types:
TT_INCOTERMSHEADERNODE type standard table of TS_INCOTERMSHEADERNODE .
  types:
     TS_INCOTERMSITEMNODE type TDS_SD_SLS_FDP_INCOTERMS .
  types:
TT_INCOTERMSITEMNODE type standard table of TS_INCOTERMSITEMNODE .
  types:
     TS_ORDERREASONNODE type TDS_SD_SLS_FDP_ORDER_REASON .
  types:
TT_ORDERREASONNODE type standard table of TS_ORDERREASONNODE .
  types:
     TS_PAYERPARTYNODE type TDS_SD_SLS_FDP_PARTNER .
  types:
TT_PAYERPARTYNODE type standard table of TS_PAYERPARTYNODE .
  types:
     TS_PRICEITEMCONDITIONNODE type TDS_SD_SLS_FDP_PRICE_COND .
  types:
TT_PRICEITEMCONDITIONNODE type standard table of TS_PRICEITEMCONDITIONNODE .
  types:
     TS_PRICETOTALCONDITIONNODE type TDS_SD_SLS_FDP_PRICE_COND .
  types:
TT_PRICETOTALCONDITIONNODE type standard table of TS_PRICETOTALCONDITIONNODE .
  types:
  begin of TS_QUERYNODE,
     LANGUAGE type C length 1,
     SALESORDER type C length 10,
     SENDERCOUNTRY type C length 3,
     ISCHANGEDOCUMENT type C length 1,
     OUTPUTREQUESTITEMUUID type C length 32,
  end of TS_QUERYNODE .
  types:
TT_QUERYNODE type standard table of TS_QUERYNODE .
  types:
     TS_REJECTEDITEMNODE type TDS_SD_SLS_FDP_REJECT_ITEM .
  types:
TT_REJECTEDITEMNODE type standard table of TS_REJECTEDITEMNODE .
  types:
     TS_SALESORDERITEMNODE type TDS_SD_SLS_FDP_ORDCONF_ITM .
  types:
TT_SALESORDERITEMNODE type standard table of TS_SALESORDERITEMNODE .
  types:
     TS_SALESORDERNODE type TDS_SD_SLS_FDP_ORDCONF_HDR .
  types:
TT_SALESORDERNODE type standard table of TS_SALESORDERNODE .
  types:
     TS_SALESORDERSUBTOTALNODE type TDS_SD_SLS_FDP_SUB_TOTAL .
  types:
TT_SALESORDERSUBTOTALNODE type standard table of TS_SALESORDERSUBTOTALNODE .
  types:
     TS_SCHEDULELINEHEADERNODE type TDS_SD_SLS_FDP_SCHEDULE_LINE .
  types:
TT_SCHEDULELINEHEADERNODE type standard table of TS_SCHEDULELINEHEADERNODE .
  types:
     TS_SCHEDULELINEITEMNODE type TDS_SD_SLS_FDP_SCHEDULE_LINE .
  types:
TT_SCHEDULELINEITEMNODE type standard table of TS_SCHEDULELINEITEMNODE .
  types:
     TS_SCHEDULELINETOTALNODE type TDS_SD_SLS_FDP_ITMSCHDLN_TOTAL .
  types:
TT_SCHEDULELINETOTALNODE type standard table of TS_SCHEDULELINETOTALNODE .
  types:
     TS_SERIALNUMBERNODE type TDS_SD_SLS_FDP_SERIAL_NUMBER .
  types:
TT_SERIALNUMBERNODE type standard table of TS_SERIALNUMBERNODE .
  types:
     TS_SHIPITEMTOPARTYNODE type TDS_SD_SLS_FDP_PARTNER .
  types:
TT_SHIPITEMTOPARTYNODE type standard table of TS_SHIPITEMTOPARTYNODE .
  types:
     TS_SHIPTOPARTYNODE type TDS_SD_SLS_FDP_PARTNER .
  types:
TT_SHIPTOPARTYNODE type standard table of TS_SHIPTOPARTYNODE .
  types:
     TS_SOLDTOPARTYNODE type TDS_SD_SLS_FDP_PARTNER .
  types:
TT_SOLDTOPARTYNODE type standard table of TS_SOLDTOPARTYNODE .
  types:
     TS_STATUSITEMNODE type TDS_SD_SLS_FDP_STATUS_ITEM .
  types:
TT_STATUSITEMNODE type standard table of TS_STATUSITEMNODE .
  types:
     TS_STATUSNODE type TDS_SD_SLS_FDP_STATUS_HEAD .
  types:
TT_STATUSNODE type standard table of TS_STATUSNODE .
  types:
     TS_TERMSOFPAYMENTITEMNODE type TDS_SD_SLS_FDP_PAYMENT_TERM .
  types:
TT_TERMSOFPAYMENTITEMNODE type standard table of TS_TERMSOFPAYMENTITEMNODE .
  types:
     TS_TERMSOFPAYMENTNODE type TDS_SD_SLS_FDP_PAYMENT_TERM .
  types:
TT_TERMSOFPAYMENTNODE type standard table of TS_TERMSOFPAYMENTNODE .
  types:
     TS_TEXTLINEHEADERNODE type TDS_SD_SLS_FDP_TEXT .
  types:
TT_TEXTLINEHEADERNODE type standard table of TS_TEXTLINEHEADERNODE .
  types:
     TS_TEXTLINEITEMNODE type TDS_SD_SLS_FDP_TEXT .
  types:
TT_TEXTLINEITEMNODE type standard table of TS_TEXTLINEITEMNODE .
  types:
  begin of TS_SO_HEADER,
     SALESORDER type VDM_SALES_ORDER,
     INVOICEDATE type ERDAT,
     QUOTATIONNUMBER type VDM_SALES_ORDER,
     QUOTATIONDATE type ERDAT,
     PAYMENTTERMS type string,
     INCOTERMS type string,
     BFADDRESS type string,
     BFPLANT type WERKS_D,
     BFSTATECODE type string,
     BFGSTIN type string,
     BFPAN type string,
     BFIEC type string,
     BTADDRESS type string,
     BTCUSTOMERCODE type string,
     BTSTATECODE type string,
     BTGSTIN type string,
     BTPAN type string,
     BTIEC type string,
     SFADDRESS type string,
     SFPLANT type string,
     SFSTATECODE type string,
     SFGSTIN type string,
     SFPAN type string,
     SFIEC type string,
     STADDRESS type string,
     STCUSTOMERCODE type string,
     STSTATECODE type string,
     STGSTIN type string,
     STPAN type string,
     STIEC type string,
     BANKDETAILS type string,
     BENEFICIARYNAME type string,
     BENEFICIARYADDRESS type string,
     BANKNAME type string,
     BRANCHADDRESS type string,
     BENEFICIARYACCNO type string,
     ACCOUNTTYPE type string,
     IFSCCODE type string,
     REMARKS type string,
  end of TS_SO_HEADER .
  types:
TT_SO_HEADER type standard table of TS_SO_HEADER .
  types:
  begin of TS_SO_ITEM,
     SALESORDER type VDM_SALES_ORDER,
     ITEMNO type string,
     CUSTOMERMATERIALCODE type string,
     RSBMATERIALCODE type MATNR,
     CUSTOMERMATERIALDESC type string,
     RSBMATERIALDESC type string,
     HSNSAC type string,
     HSNSACDESC type string,
     QUANTITY type string,
     UOM type string,
     UNITPRICE type string,
     DISCOUNTPERCENT type string,
     DISCOUNTVALUE type string,
     VALUEAFTERDISCOUNT type string,
     TOTALBASICVALUE type string,
     TOTALDISCOUNT type string,
     TOTALVALUEAFTERDISCOUNT type string,
     FREIGHTCHARGES type string,
     PACKAGINGCHARGES type string,
     TOOLARMOTIZATION type string,
     TAXABLEVALUE type string,
     IGSTPERCENT type string,
     IGSTVALUE type string,
     CGSTPERCENT type string,
     CGSTVALUE type string,
     SGSTPERCENT type string,
     SGSTVALUE type string,
     TCSPERCENT type string,
     TCSVALUE type string,
     LESSTAXABLEVALUE type string,
     TOTALINVOICEVALUE type string,
     TOTALGSTINWORDS type string,
     TOTALINVOICEINWORDS type string,
     DISCOUNTFLAG type string,
  end of TS_SO_ITEM .
  types:
TT_SO_ITEM type standard table of TS_SO_ITEM .
  types:
  begin of TS_SO_ITEM_WITHOUT_DISCOUNT,
     SALESORDER type C length 10,
     ITEMNO type string,
     CUSTOMERMATERIALCODE type string,
     RSBMATERIALCODE type string,
     CUSTOMERMATERIALDESC type string,
     RSBMATERIALDESC type string,
     HSNSAC type string,
     HSNSACDESC type string,
     QUANTITY type string,
     UOM type string,
     UNITPRICE type string,
     DISCOUNTPERCENT type string,
     DISCOUNTVALUE type string,
     VALUEAFTERDISCOUNT type string,
     TOTALBASICVALUE type string,
     TOTALDISCOUNT type string,
     TOTALVALUEAFTERDISCOUNT type string,
     FREIGHTCHARGES type string,
     PACKAGINGCHARGES type string,
     TOOLARMOTIZATION type string,
     TAXABLEVALUE type string,
     IGSTPERCENT type string,
     IGSTVALUE type string,
     CGSTPERCENT type string,
     CGSTVALUE type string,
     SGSTPERCENT type string,
     SGSTVALUE type string,
     TCSPERCENT type string,
     TCSVALUE type string,
     LESSTAXABLEVALUE type string,
     TOTALINVOICEVALUE type string,
     TOTALGSTINWORDS type string,
     TOTALINVOICEINWORDS type string,
     DISCOUNTFLAG type string,
  end of TS_SO_ITEM_WITHOUT_DISCOUNT .
  types:
TT_SO_ITEM_WITHOUT_DISCOUNT type standard table of TS_SO_ITEM_WITHOUT_DISCOUNT .

  constants GC_TEXTLINEITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'TextLineItemNode' ##NO_TEXT.
  constants GC_TEXTLINEHEADERNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'TextLineHeaderNode' ##NO_TEXT.
  constants GC_TERMSOFPAYMENTNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'TermsOfPaymentNode' ##NO_TEXT.
  constants GC_TERMSOFPAYMENTITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'TermsOfPaymentItemNode' ##NO_TEXT.
  constants GC_STATUSNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'StatusNode' ##NO_TEXT.
  constants GC_STATUSITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'StatusItemNode' ##NO_TEXT.
  constants GC_SO_ITEM_WITHOUT_DISCOUNT type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'SO_Item_Without_Discount' ##NO_TEXT.
  constants GC_SO_ITEM type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'SO_Item' ##NO_TEXT.
  constants GC_SO_HEADER type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'SO_Header' ##NO_TEXT.
  constants GC_SOLDTOPARTYNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'SoldToPartyNode' ##NO_TEXT.
  constants GC_SHIPTOPARTYNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ShipToPartyNode' ##NO_TEXT.
  constants GC_SHIPITEMTOPARTYNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ShipItemToPartyNode' ##NO_TEXT.
  constants GC_SERIALNUMBERNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'SerialNumberNode' ##NO_TEXT.
  constants GC_SCHEDULELINETOTALNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ScheduleLineTotalNode' ##NO_TEXT.
  constants GC_SCHEDULELINEITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ScheduleLineItemNode' ##NO_TEXT.
  constants GC_SCHEDULELINEHEADERNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ScheduleLineHeaderNode' ##NO_TEXT.
  constants GC_SALESORDERSUBTOTALNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'SalesOrderSubTotalNode' ##NO_TEXT.
  constants GC_SALESORDERNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'SalesOrderNode' ##NO_TEXT.
  constants GC_SALESORDERITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'SalesOrderItemNode' ##NO_TEXT.
  constants GC_REJECTEDITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'RejectedItemNode' ##NO_TEXT.
  constants GC_QUERYNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'QueryNode' ##NO_TEXT.
  constants GC_PRICETOTALCONDITIONNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'PriceTotalConditionNode' ##NO_TEXT.
  constants GC_PRICEITEMCONDITIONNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'PriceItemConditionNode' ##NO_TEXT.
  constants GC_PAYERPARTYNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'PayerPartyNode' ##NO_TEXT.
  constants GC_ORDERREASONNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'OrderReasonNode' ##NO_TEXT.
  constants GC_INCOTERMSITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'IncotermsItemNode' ##NO_TEXT.
  constants GC_INCOTERMSHEADERNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'IncotermsHeaderNode' ##NO_TEXT.
  constants GC_CONFIGURATIONITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ConfigurationItemNode' ##NO_TEXT.
  constants GC_COMPANYNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'CompanyNode' ##NO_TEXT.
  constants GC_CHGDOCSUMITMTXTNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ChgDocSumItmTxtNode' ##NO_TEXT.
  constants GC_CHGDOCSUMHDRTXTNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ChgDocSumHdrTxtNode' ##NO_TEXT.
  constants GC_CHGDOCSUMDTLTXTNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ChgDocSumDtlTxtNode' ##NO_TEXT.
  constants GC_CHGDOCITMDTLTXTNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'ChgDocItmDtlTxtNode' ##NO_TEXT.
  constants GC_BILLTOPARTYNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'BillToPartyNode' ##NO_TEXT.
  constants GC_BILLINGPERIODICITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'BillingPeriodicItemNode' ##NO_TEXT.
  constants GC_BILLINGMILESTONEITEMNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'BillingMilestoneItemNode' ##NO_TEXT.
  constants GC_ADDITIONALITEMTEXTNODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'AdditionalItemTextNode' ##NO_TEXT.
  constants GC_ADDITIONALITEMTEXTLINENODE type /IWBEP/IF_MGW_MED_ODATA_TYPES=>TY_E_MED_ENTITY_NAME value 'AdditionalItemTextLineNode' ##NO_TEXT.

  methods GET_EXTENDED_MODEL
  final
    exporting
      !EV_EXTENDED_SERVICE type /IWBEP/MED_GRP_TECHNICAL_NAME
      !EV_EXT_SERVICE_VERSION type /IWBEP/MED_GRP_VERSION
      !EV_EXTENDED_MODEL type /IWBEP/MED_MDL_TECHNICAL_NAME
      !EV_EXT_MODEL_VERSION type /IWBEP/MED_MDL_VERSION
    raising
      /IWBEP/CX_MGW_MED_EXCEPTION .
  methods LOAD_TEXT_ELEMENTS
  final
    returning
      value(RT_TEXT_ELEMENTS) type TT_TEXT_ELEMENTS
    raising
      /IWBEP/CX_MGW_MED_EXCEPTION .

  methods DEFINE
    redefinition .
  methods GET_LAST_MODIFIED
    redefinition .
protected section.
private section.

  methods CREATE_NEW_ARTIFACTS
    raising
      /IWBEP/CX_MGW_MED_EXCEPTION .
ENDCLASS.



CLASS ZCL_ZSALES_ORDER_CONFO_MPC IMPLEMENTATION.


  method CREATE_NEW_ARTIFACTS.
*&---------------------------------------------------------------------*
*&           Generated code for the MODEL PROVIDER BASE CLASS          &*
*&                                                                     &*
*&  !!!NEVER MODIFY THIS CLASS. IN CASE YOU WANT TO CHANGE THE MODEL   &*
*&        DO THIS IN THE MODEL PROVIDER SUBCLASS!!!                    &*
*&                                                                     &*
*&---------------------------------------------------------------------*


DATA:
  lo_entity_type    TYPE REF TO /iwbep/if_mgw_odata_entity_typ,                      "#EC NEEDED
  lo_complex_type   TYPE REF TO /iwbep/if_mgw_odata_cmplx_type,                      "#EC NEEDED
  lo_property       TYPE REF TO /iwbep/if_mgw_odata_property,                        "#EC NEEDED
  lo_association    TYPE REF TO /iwbep/if_mgw_odata_assoc,                           "#EC NEEDED
  lo_assoc_set      TYPE REF TO /iwbep/if_mgw_odata_assoc_set,                       "#EC NEEDED
  lo_ref_constraint TYPE REF TO /iwbep/if_mgw_odata_ref_constr,                      "#EC NEEDED
  lo_nav_property   TYPE REF TO /iwbep/if_mgw_odata_nav_prop,                        "#EC NEEDED
  lo_action         TYPE REF TO /iwbep/if_mgw_odata_action,                          "#EC NEEDED
  lo_parameter      TYPE REF TO /iwbep/if_mgw_odata_property,                        "#EC NEEDED
  lo_entity_set     TYPE REF TO /iwbep/if_mgw_odata_entity_set.                      "#EC NEEDED


***********************************************************************************************************************************
*   ENTITY - SO_Header
***********************************************************************************************************************************
lo_entity_type = model->create_entity_type( iv_entity_type_name = 'SO_Header' iv_def_entity_set = abap_false ). "#EC NOTEXT

***********************************************************************************************************************************
*Properties
***********************************************************************************************************************************

lo_property = lo_entity_type->create_property( iv_property_name = 'SalesOrder' iv_abap_fieldname = 'SALESORDER' ). "#EC NOTEXT
lo_property->set_is_key( ).
lo_property->set_type_edm_string( ).
lo_property->set_maxlength( iv_max_length = 10 ).
lo_property->set_conversion_exit( 'ALPHA' ). "#EC NOTEXT
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'InvoiceDate' iv_abap_fieldname = 'INVOICEDATE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'QuotationNumber' iv_abap_fieldname = 'QUOTATIONNUMBER' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_conversion_exit( 'ALPHA' ). "#EC NOTEXT
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'QuotationDate' iv_abap_fieldname = 'QUOTATIONDATE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'PaymentTerms' iv_abap_fieldname = 'PAYMENTTERMS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'IncoTerms' iv_abap_fieldname = 'INCOTERMS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BFAddress' iv_abap_fieldname = 'BFADDRESS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BFPlant' iv_abap_fieldname = 'BFPLANT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BFStateCode' iv_abap_fieldname = 'BFSTATECODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BFGstin' iv_abap_fieldname = 'BFGSTIN' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BFPan' iv_abap_fieldname = 'BFPAN' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BFIec' iv_abap_fieldname = 'BFIEC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BTAddress' iv_abap_fieldname = 'BTADDRESS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BTCustomerCode' iv_abap_fieldname = 'BTCUSTOMERCODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BTStateCode' iv_abap_fieldname = 'BTSTATECODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BTGstin' iv_abap_fieldname = 'BTGSTIN' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BTPan' iv_abap_fieldname = 'BTPAN' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BTIec' iv_abap_fieldname = 'BTIEC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SFAddress' iv_abap_fieldname = 'SFADDRESS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SFPlant' iv_abap_fieldname = 'SFPLANT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SFStateCode' iv_abap_fieldname = 'SFSTATECODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SFGstin' iv_abap_fieldname = 'SFGSTIN' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SFPan' iv_abap_fieldname = 'SFPAN' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SFIec' iv_abap_fieldname = 'SFIEC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'STAddress' iv_abap_fieldname = 'STADDRESS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'STCustomerCode' iv_abap_fieldname = 'STCUSTOMERCODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'STStateCode' iv_abap_fieldname = 'STSTATECODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'STGstin' iv_abap_fieldname = 'STGSTIN' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'STPan' iv_abap_fieldname = 'STPAN' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'STIec' iv_abap_fieldname = 'STIEC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BankDetails' iv_abap_fieldname = 'BANKDETAILS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BeneficiaryName' iv_abap_fieldname = 'BENEFICIARYNAME' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BeneficiaryAddress' iv_abap_fieldname = 'BENEFICIARYADDRESS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BankName' iv_abap_fieldname = 'BANKNAME' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BranchAddress' iv_abap_fieldname = 'BRANCHADDRESS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'BeneficiaryAccNo' iv_abap_fieldname = 'BENEFICIARYACCNO' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'AccountType' iv_abap_fieldname = 'ACCOUNTTYPE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'IFSCCode' iv_abap_fieldname = 'IFSCCODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'Remarks' iv_abap_fieldname = 'REMARKS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).

lo_entity_type->bind_structure( iv_structure_name  = 'ZCL_ZSALES_ORDER_CONFO_MPC=>TS_SO_HEADER' ). "#EC NOTEXT


lo_entity_type = model->create_entity_type( iv_entity_type_name = 'SO_Item' iv_def_entity_set = abap_false ). "#EC NOTEXT

***********************************************************************************************************************************
*Properties
***********************************************************************************************************************************

lo_property = lo_entity_type->create_property( iv_property_name = 'SalesOrder' iv_abap_fieldname = 'SALESORDER' ). "#EC NOTEXT
lo_property->set_is_key( ).
lo_property->set_type_edm_string( ).
lo_property->set_maxlength( iv_max_length = 10 ).
lo_property->set_conversion_exit( 'ALPHA' ). "#EC NOTEXT
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'ItemNo' iv_abap_fieldname = 'ITEMNO' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'CustomerMaterialCode' iv_abap_fieldname = 'CUSTOMERMATERIALCODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'RSBMaterialCode' iv_abap_fieldname = 'RSBMATERIALCODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_conversion_exit( 'MATN1' ). "#EC NOTEXT
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'CustomerMaterialDesc' iv_abap_fieldname = 'CUSTOMERMATERIALDESC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'RSBMaterialDesc' iv_abap_fieldname = 'RSBMATERIALDESC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'HsnSac' iv_abap_fieldname = 'HSNSAC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'HsnSacDesc' iv_abap_fieldname = 'HSNSACDESC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'Quantity' iv_abap_fieldname = 'QUANTITY' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'UOM' iv_abap_fieldname = 'UOM' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'UnitPrice' iv_abap_fieldname = 'UNITPRICE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'DiscountPercent' iv_abap_fieldname = 'DISCOUNTPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'DiscountValue' iv_abap_fieldname = 'DISCOUNTVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'ValueAfterDiscount' iv_abap_fieldname = 'VALUEAFTERDISCOUNT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalBasicValue' iv_abap_fieldname = 'TOTALBASICVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalDiscount' iv_abap_fieldname = 'TOTALDISCOUNT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalValueAfterDiscount' iv_abap_fieldname = 'TOTALVALUEAFTERDISCOUNT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'FreightCharges' iv_abap_fieldname = 'FREIGHTCHARGES' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'PackagingCharges' iv_abap_fieldname = 'PACKAGINGCHARGES' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'ToolArmotization' iv_abap_fieldname = 'TOOLARMOTIZATION' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TaxableValue' iv_abap_fieldname = 'TAXABLEVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'IgstPercent' iv_abap_fieldname = 'IGSTPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'IgstValue' iv_abap_fieldname = 'IGSTVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'cgstPercent' iv_abap_fieldname = 'CGSTPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'CgstValue' iv_abap_fieldname = 'CGSTVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SgstPercent' iv_abap_fieldname = 'SGSTPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SgstValue' iv_abap_fieldname = 'SGSTVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TcsPercent' iv_abap_fieldname = 'TCSPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TcsValue' iv_abap_fieldname = 'TCSVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'LessTaxableValue' iv_abap_fieldname = 'LESSTAXABLEVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalInvoiceValue' iv_abap_fieldname = 'TOTALINVOICEVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalGstInWords' iv_abap_fieldname = 'TOTALGSTINWORDS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalInvoiceInWords' iv_abap_fieldname = 'TOTALINVOICEINWORDS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'DiscountFlag' iv_abap_fieldname = 'DISCOUNTFLAG' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).

lo_entity_type->bind_structure( iv_structure_name  = 'ZCL_ZSALES_ORDER_CONFO_MPC=>TS_SO_ITEM' ). "#EC NOTEXT


lo_entity_type = model->create_entity_type( iv_entity_type_name = 'SO_Item_Without_Discount' iv_def_entity_set = abap_false ). "#EC NOTEXT

***********************************************************************************************************************************
*Properties
***********************************************************************************************************************************

lo_property = lo_entity_type->create_property( iv_property_name = 'SalesOrder' iv_abap_fieldname = 'SALESORDER' ). "#EC NOTEXT
lo_property->set_is_key( ).
lo_property->set_type_edm_string( ).
lo_property->set_maxlength( iv_max_length = 10 ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'ItemNo' iv_abap_fieldname = 'ITEMNO' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'CustomerMaterialCode' iv_abap_fieldname = 'CUSTOMERMATERIALCODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'RSBMaterialCode' iv_abap_fieldname = 'RSBMATERIALCODE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'CustomerMaterialDesc' iv_abap_fieldname = 'CUSTOMERMATERIALDESC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'RSBMaterialDesc' iv_abap_fieldname = 'RSBMATERIALDESC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'HsnSac' iv_abap_fieldname = 'HSNSAC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'HsnSacDesc' iv_abap_fieldname = 'HSNSACDESC' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'Quantity' iv_abap_fieldname = 'QUANTITY' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'UOM' iv_abap_fieldname = 'UOM' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'UnitPrice' iv_abap_fieldname = 'UNITPRICE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'DiscountPercent' iv_abap_fieldname = 'DISCOUNTPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'DiscountValue' iv_abap_fieldname = 'DISCOUNTVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'ValueAfterDiscount' iv_abap_fieldname = 'VALUEAFTERDISCOUNT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalBasicValue' iv_abap_fieldname = 'TOTALBASICVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalDiscount' iv_abap_fieldname = 'TOTALDISCOUNT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalValueAfterDiscount' iv_abap_fieldname = 'TOTALVALUEAFTERDISCOUNT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'FreightCharges' iv_abap_fieldname = 'FREIGHTCHARGES' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'PackagingCharges' iv_abap_fieldname = 'PACKAGINGCHARGES' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'ToolArmotization' iv_abap_fieldname = 'TOOLARMOTIZATION' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TaxableValue' iv_abap_fieldname = 'TAXABLEVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'IgstPercent' iv_abap_fieldname = 'IGSTPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'IgstValue' iv_abap_fieldname = 'IGSTVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'cgstPercent' iv_abap_fieldname = 'CGSTPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'CgstValue' iv_abap_fieldname = 'CGSTVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SgstPercent' iv_abap_fieldname = 'SGSTPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'SgstValue' iv_abap_fieldname = 'SGSTVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TcsPercent' iv_abap_fieldname = 'TCSPERCENT' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TcsValue' iv_abap_fieldname = 'TCSVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'LessTaxableValue' iv_abap_fieldname = 'LESSTAXABLEVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalInvoiceValue' iv_abap_fieldname = 'TOTALINVOICEVALUE' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalGstInWords' iv_abap_fieldname = 'TOTALGSTINWORDS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'TotalInvoiceInWords' iv_abap_fieldname = 'TOTALINVOICEINWORDS' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).
lo_property = lo_entity_type->create_property( iv_property_name = 'DiscountFlag' iv_abap_fieldname = 'DISCOUNTFLAG' ). "#EC NOTEXT
lo_property->set_type_edm_string( ).
lo_property->set_creatable( abap_false ).
lo_property->set_updatable( abap_false ).
lo_property->set_sortable( abap_false ).
lo_property->set_nullable( abap_false ).
lo_property->set_filterable( abap_false ).

lo_entity_type->bind_structure( iv_structure_name  = 'ZCL_ZSALES_ORDER_CONFO_MPC=>TS_SO_ITEM_WITHOUT_DISCOUNT' ). "#EC NOTEXT


***********************************************************************************************************************************
*   ENTITY SETS
***********************************************************************************************************************************
lo_entity_type = model->get_entity_type( iv_entity_name = 'SO_Header' ). "#EC NOTEXT
lo_entity_set = lo_entity_type->create_entity_set( 'SO_HeaderSet' ). "#EC NOTEXT

lo_entity_set->set_creatable( abap_false ).
lo_entity_set->set_updatable( abap_false ).
lo_entity_set->set_deletable( abap_false ).

lo_entity_set->set_pageable( abap_false ).
lo_entity_set->set_addressable( abap_false ).
lo_entity_set->set_has_ftxt_search( abap_false ).
lo_entity_set->set_subscribable( abap_false ).
lo_entity_set->set_filter_required( abap_false ).
lo_entity_type = model->get_entity_type( iv_entity_name = 'SO_Item' ). "#EC NOTEXT
lo_entity_set = lo_entity_type->create_entity_set( 'SO_ItemSet' ). "#EC NOTEXT

lo_entity_set->set_creatable( abap_false ).
lo_entity_set->set_updatable( abap_false ).
lo_entity_set->set_deletable( abap_false ).

lo_entity_set->set_pageable( abap_false ).
lo_entity_set->set_addressable( abap_false ).
lo_entity_set->set_has_ftxt_search( abap_false ).
lo_entity_set->set_subscribable( abap_false ).
lo_entity_set->set_filter_required( abap_false ).
lo_entity_type = model->get_entity_type( iv_entity_name = 'SO_Item_Without_Discount' ). "#EC NOTEXT
lo_entity_set = lo_entity_type->create_entity_set( 'SO_Item_Without_DiscountSet' ). "#EC NOTEXT

lo_entity_set->set_creatable( abap_false ).
lo_entity_set->set_updatable( abap_false ).
lo_entity_set->set_deletable( abap_false ).

lo_entity_set->set_pageable( abap_false ).
lo_entity_set->set_addressable( abap_false ).
lo_entity_set->set_has_ftxt_search( abap_false ).
lo_entity_set->set_subscribable( abap_false ).
lo_entity_set->set_filter_required( abap_false ).


***********************************************************************************************************************************
*   new_associations
***********************************************************************************************************************************

 lo_association = model->create_association(
                            iv_association_name = 'SalesOrderHeader' "#EC NOTEXT
                            iv_left_type        = 'SalesOrderNode' "#EC NOTEXT
                            iv_right_type       = 'SO_Header' "#EC NOTEXT
                            iv_right_card       = '1' "#EC NOTEXT
                            iv_left_card        = '1' ). "#EC NOTEXT
* Referential constraint for association - SalesOrderHeader
lo_ref_constraint = lo_association->create_ref_constraint( ).
lo_ref_constraint->add_property( iv_principal_property = 'SalesOrder'   iv_dependent_property = 'SalesOrder' )."#EC NOTEXT
* Association Sets for association - SalesOrderHeader
lo_assoc_set = lo_association->create_assoc_set( iv_assoc_set_name = 'SalesOrderHeaderSet' ). "#EC NOTEXT
 lo_association = model->create_association(
                            iv_association_name = 'SalesOrderItems' "#EC NOTEXT
                            iv_left_type        = 'SalesOrderNode' "#EC NOTEXT
                            iv_right_type       = 'SO_Item' "#EC NOTEXT
                            iv_right_card       = 'N' "#EC NOTEXT
                            iv_left_card        = '1' ). "#EC NOTEXT
* Referential constraint for association - SalesOrderItems
lo_ref_constraint = lo_association->create_ref_constraint( ).
lo_ref_constraint->add_property( iv_principal_property = 'SalesOrder'   iv_dependent_property = 'SalesOrder' )."#EC NOTEXT
* Association Sets for association - SalesOrderItems
lo_assoc_set = lo_association->create_assoc_set( iv_assoc_set_name = 'SalesOrderItemSet' ). "#EC NOTEXT
 lo_association = model->create_association(
                            iv_association_name = 'SalesOrderItemWithoutDiscount' "#EC NOTEXT
                            iv_left_type        = 'SalesOrderNode' "#EC NOTEXT
                            iv_right_type       = 'SO_Item_Without_Discount' "#EC NOTEXT
                            iv_right_card       = 'N' "#EC NOTEXT
                            iv_left_card        = '1' ). "#EC NOTEXT
* Referential constraint for association - SalesOrderItemWithoutDiscount
lo_ref_constraint = lo_association->create_ref_constraint( ).
lo_ref_constraint->add_property( iv_principal_property = 'SalesOrder'   iv_dependent_property = 'SalesOrder' )."#EC NOTEXT
* Association Sets for association - SalesOrderItemWithoutDiscount
lo_assoc_set = lo_association->create_assoc_set( iv_assoc_set_name = 'SalesOrderItemWithoutDiscountSet' ). "#EC NOTEXT


* Navigation Properties for entity - SO_Header
lo_entity_type = model->get_entity_type( iv_entity_name = 'SO_Header' ). "#EC NOTEXT
lo_nav_property = lo_entity_type->create_navigation_property( iv_property_name  = 'SalesOrderNode' "#EC NOTEXT
                                                          iv_association_name = 'SalesOrderHeader' ). "#EC NOTEXT
* Navigation Properties for entity - SO_Item
lo_entity_type = model->get_entity_type( iv_entity_name = 'SO_Item' ). "#EC NOTEXT
lo_nav_property = lo_entity_type->create_navigation_property( iv_property_name  = 'SalesOrderNode' "#EC NOTEXT
                                                          iv_association_name = 'SalesOrderItems' ). "#EC NOTEXT
* Navigation Properties for entity - SO_Item_Without_Discount
lo_entity_type = model->get_entity_type( iv_entity_name = 'SO_Item_Without_Discount' ). "#EC NOTEXT
lo_nav_property = lo_entity_type->create_navigation_property( iv_property_name  = 'SalesOrderNode' "#EC NOTEXT
                                                          iv_association_name = 'SalesOrderItemWithoutDiscount' ). "#EC NOTEXT


   lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderNode' ). "#EC NOTEXT
   lo_nav_property = lo_entity_type->create_navigation_property( iv_property_name  = 'SO_ItemSet' "#EC NOTEXT
                                                          iv_association_name = 'SalesOrderItems' ). "#EC NOTEXT
   lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderNode' ). "#EC NOTEXT
   lo_nav_property = lo_entity_type->create_navigation_property( iv_property_name  = 'SO_Header' "#EC NOTEXT
                                                          iv_association_name = 'SalesOrderHeader' ). "#EC NOTEXT
   lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderNode' ). "#EC NOTEXT
   lo_nav_property = lo_entity_type->create_navigation_property( iv_property_name  = 'SO_Item_Without_DiscountSet' "#EC NOTEXT
                                                          iv_association_name = 'SalesOrderItemWithoutDiscount' ). "#EC NOTEXT
  endmethod.


  method DEFINE.
*&---------------------------------------------------------------------*
*&           Generated code for the MODEL PROVIDER BASE CLASS          &*
*&                                                                     &*
*&  !!!NEVER MODIFY THIS CLASS. IN CASE YOU WANT TO CHANGE THE MODEL   &*
*&        DO THIS IN THE MODEL PROVIDER SUBCLASS!!!                    &*
*&                                                                     &*
*&---------------------------------------------------------------------*


data:
  lo_entity_type    type ref to /iwbep/if_mgw_odata_entity_typ, "#EC NEEDED
  lo_complex_type   type ref to /iwbep/if_mgw_odata_cmplx_type, "#EC NEEDED
  lo_property       type ref to /iwbep/if_mgw_odata_property, "#EC NEEDED
  lo_association    type ref to /iwbep/if_mgw_odata_assoc,  "#EC NEEDED
  lo_assoc_set      type ref to /iwbep/if_mgw_odata_assoc_set, "#EC NEEDED
  lo_ref_constraint type ref to /iwbep/if_mgw_odata_ref_constr, "#EC NEEDED
  lo_nav_property   type ref to /iwbep/if_mgw_odata_nav_prop, "#EC NEEDED
  lo_action         type ref to /iwbep/if_mgw_odata_action, "#EC NEEDED
  lo_parameter      type ref to /iwbep/if_mgw_odata_property, "#EC NEEDED
  lo_entity_set     type ref to /iwbep/if_mgw_odata_entity_set, "#EC NEEDED
  lo_complex_prop   type ref to /iwbep/if_mgw_odata_cmplx_prop. "#EC NEEDED

* Extend the model
model->extend_model( iv_model_name = 'FDP_V1_ORDER_CONFIRM_MDL' iv_model_version = '0001' ). "#EC NOTEXT

model->set_schema_namespace( 'FDP_V1_ORDER_CONFIRM_SRV' ).


*Disable selected properties in a entity type
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'ChgDocItmDtlTxtNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'SalesDocument' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'ChgDocItmDtlTxtNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'SalesDocumentItem' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'ChgDocSumDtlTxtNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'SalesDocument' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'ChgDocSumDtlTxtNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'SalesDocumentItem' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'ChgDocSumHdrTxtNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'SalesDocument' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'ChgDocSumItmTxtNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'SalesDocument' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderItemNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'ZZ1_KDMAT_SDI' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderItemNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'ZZ1_KDMAT_SDIF' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'ZZ1_BSTDK_SDH' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'ZZ1_HOUSEBANKID_SDH' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'ZZ1_HOUSEBANKID_SDHF' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'ZZ1_HOUSEBANKID_SDHT' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
try.
lo_entity_type = model->get_entity_type( iv_entity_name = 'SalesOrderNode' ). "#EC NOTEXT
lo_property    = lo_entity_type->get_property( iv_property_name = 'ZZ1_BSTDK_SDHF' ). "#EC NOTEXT
lo_property->set_disabled( iv_disabled = abap_true ).
CATCH /iwbep/cx_mgw_med_exception.
*  No Action was taken as the OData Element is not a part of redefined service
ENDTRY.
* New artifacts have been created in the service builder after the redefinition of service
create_new_artifacts( ).
  endmethod.


  method GET_EXTENDED_MODEL.
*&---------------------------------------------------------------------*
*&           Generated code for the MODEL PROVIDER BASE CLASS          &*
*&                                                                     &*
*&  !!!NEVER MODIFY THIS CLASS. IN CASE YOU WANT TO CHANGE THE MODEL   &*
*&        DO THIS IN THE MODEL PROVIDER SUBCLASS!!!                    &*
*&                                                                     &*
*&---------------------------------------------------------------------*



ev_extended_service  = 'FDP_V1_ORDER_CONFIRM_SRV'.                "#EC NOTEXT
ev_ext_service_version = '0001'.               "#EC NOTEXT
ev_extended_model    = 'FDP_V1_ORDER_CONFIRM_MDL'.                    "#EC NOTEXT
ev_ext_model_version = '0001'.                   "#EC NOTEXT
  endmethod.


  method GET_LAST_MODIFIED.
*&---------------------------------------------------------------------*
*&           Generated code for the MODEL PROVIDER BASE CLASS          &*
*&                                                                     &*
*&  !!!NEVER MODIFY THIS CLASS. IN CASE YOU WANT TO CHANGE THE MODEL   &*
*&        DO THIS IN THE MODEL PROVIDER SUBCLASS!!!                    &*
*&                                                                     &*
*&---------------------------------------------------------------------*


  constants: lc_gen_date_time type timestamp value '20250806115846'. "#EC NOTEXT
rv_last_modified = super->get_last_modified( ).
IF rv_last_modified LT lc_gen_date_time.
  rv_last_modified = lc_gen_date_time.
ENDIF.
  endmethod.


  method LOAD_TEXT_ELEMENTS.
*&---------------------------------------------------------------------*
*&           Generated code for the MODEL PROVIDER BASE CLASS          &*
*&                                                                     &*
*&  !!!NEVER MODIFY THIS CLASS. IN CASE YOU WANT TO CHANGE THE MODEL   &*
*&        DO THIS IN THE MODEL PROVIDER SUBCLASS!!!                    &*
*&                                                                     &*
*&---------------------------------------------------------------------*


data:
  lo_entity_type    type ref to /iwbep/if_mgw_odata_entity_typ,           "#EC NEEDED
  lo_complex_type   type ref to /iwbep/if_mgw_odata_cmplx_type,           "#EC NEEDED
  lo_property       type ref to /iwbep/if_mgw_odata_property,             "#EC NEEDED
  lo_association    type ref to /iwbep/if_mgw_odata_assoc,                "#EC NEEDED
  lo_assoc_set      type ref to /iwbep/if_mgw_odata_assoc_set,            "#EC NEEDED
  lo_ref_constraint type ref to /iwbep/if_mgw_odata_ref_constr,           "#EC NEEDED
  lo_nav_property   type ref to /iwbep/if_mgw_odata_nav_prop,             "#EC NEEDED
  lo_action         type ref to /iwbep/if_mgw_odata_action,               "#EC NEEDED
  lo_parameter      type ref to /iwbep/if_mgw_odata_property,             "#EC NEEDED
  lo_entity_set     type ref to /iwbep/if_mgw_odata_entity_set.           "#EC NEEDED


DATA:
     ls_text_element TYPE ts_text_element.                   "#EC NEEDED
  endmethod.
ENDCLASS.
*********************************************************************************

class ZCL_ZSALES_ORDER_CONFO_MPC_EXT definition
  public
  inheriting from ZCL_ZSALES_ORDER_CONFO_MPC
  create public .

public section.
protected section.
private section.
ENDCLASS.



CLASS ZCL_ZSALES_ORDER_CONFO_MPC_EXT IMPLEMENTATION.
ENDCLASS.
*****************************************************************************

class ZSALES_ORDER_CALLBACK_CLASS definition
  public
  final
  create public .

public section.

  interfaces IF_BADI_INTERFACE .
  interfaces IF_APOC_AUTHORITY .
  interfaces IF_APOC_COMMON_API .
  interfaces IF_SOMU_BADI_FORM_MASTER .
  interfaces IF_APOC_OR_ITEM_VALIDATION .

  class-methods CLASS_CONSTRUCTOR .
  protected section.
private section.

  data MO_APOC_OR_H_API type ref to IF_APOC_OR_H_API .
  class-data GC_APPL_OBJECT_TYPE type APOC_APPL_OBJECT_TYPE value 'SALES_DOCUMENT' ##NO_TEXT.
  class-data SO_SD_SLS_OM type ref to IF_SD_SLS_OM .

  methods SET_TRANSIENT_DATA
    importing
      !IO_SLS_DATA_REF type ref to CL_SLS_DATA_REF
      !IR_OUTPUT_REQUEST_INFO type ref to IF_APOC_COMMON_API=>TY_GS_OUTPUT_REQUEST_INFO .
ENDCLASS.



CLASS ZSALES_ORDER_CALLBACK_CLASS IMPLEMENTATION.


  method CLASS_CONSTRUCTOR.
    so_sd_sls_om = cl_sd_sls_om_factory=>get_output_management( ).
  endmethod.


  method IF_APOC_AUTHORITY~CHECK.

    data: ls_vbak type vbak.

    rv_authorized = abap_false.        " assume no authorization for requested action until we know otherwise

    if cl_sd_sls_fdp_v1_util=>so_instance->has_transient_data( ).
      cl_sd_sls_fdp_v1_util=>so_instance->get_transient_data( importing ev_vbak = data(ls_vbakvb) ).
      move-corresponding ls_vbakvb to ls_vbak.
    else.
      if is_data-appl_object_id <> if_sd_sls_om=>mc_om-temporary_document_key.
        select single vkorg vtweg spart auart from vbak into corresponding fields of ls_vbak
          where vbeln = is_data-appl_object_id.
        assert sy-subrc = 0.
      endif.
    endif.

    rv_authorized = cl_sd_sls_om_factory=>get_output_management( )->has_authorization(
      iv_sales_organization   = ls_vbak-vkorg
      iv_distribution_channel = ls_vbak-vtweg
      iv_division             = ls_vbak-spart
      iv_order_type           = ls_vbak-auart
      iv_check_print          = abap_false "output should be processed even though the user does not have auth to see it
      iv_action_name          = iv_action_name
      iv_activity             = cond #( when iv_activity = if_apoc_or_c=>gc_activity_display
                                        then if_apoc_or_c=>gc_activity_display
                                        else if_apoc_or_c=>gc_activity_change ) ).

  endmethod.


  method IF_APOC_COMMON_API~CHECK_AUTHORITY.


    data lo_badi_authority type ref to apoc_authority.
    data ls_data           type if_apoc_authority=>ty_s_auth_data.
    data lv_activity       type activ_auth.
    data lo_msg_collector  type ref to if_apoc_t100_message_collector.

    " Map data
    ls_data-appl_object_type = is_or_item-appl_object_type.
    ls_data-appl_object_id   = is_or_item-appl_object_id.

    select single outputparametertext from i_outputrequest into @ls_data-output_parameter
      where outputrequestuuid = @is_or_item-parent_key.

    " Map message collector
    lo_msg_collector = cast #( io_msg_collector ).

    " Map Action Key to Activity
    case iv_action_key.
      when if_apoc_output_request_c=>sc_action-item-get_document.
        lv_activity = if_apoc_or_c=>gc_activity_display.
      when if_apoc_output_request_c=>sc_action-item-issue_output.
        lv_activity = if_apoc_or_c=>gc_activity_change.
      when if_apoc_output_request_c=>sc_action-item-preview.
        lv_activity = if_apoc_or_c=>gc_activity_display.
      when if_apoc_output_request_c=>sc_action-item-resend.
        case is_or_item-status.
          when if_apoc_or_c=>gc_output_status_error.
            lv_activity = if_apoc_or_c=>gc_activity_change.
          when if_apoc_or_c=>gc_output_status_successful.
            lv_activity = if_apoc_or_c=>gc_activity_create.
        endcase.
    endcase.

    get badi lo_badi_authority filters appl_object_type = is_or_item-appl_object_type.
    call badi lo_badi_authority->check
      exporting
        is_data                   = ls_data
        iv_activity               = lv_activity
        io_t100_message_collector = lo_msg_collector
      receiving
        rv_authorized             = rv_authorized.

  endmethod.


  METHOD IF_APOC_COMMON_API~GET_DATA_FOR_ROLE.

    DATA:
      lv_addrhandle    TYPE ad_handle,
*      lv_adrnr         TYPE adrnr,
      ls_address_value TYPE addr1_val,
      ls_comm_data     TYPE sza5_d0700,
      ls_vbak          TYPE vbakvb,
      lt_adsmtp        TYPE TABLE OF adsmtp,
      lt_vbpavb        TYPE TABLE OF vbpavb,
      ls_vbpa          TYPE vbpa,
      lv_language      TYPE spras,
      lv_lngcor        TYPE bu_langu_corr,   " Partner: Correspondence Language
      ls_vbadr         TYPE vbadr
      .

    IF cl_sd_sls_fdp_v1_util=>so_instance->has_transient_data( ).
      cl_sd_sls_fdp_v1_util=>so_instance->get_transient_data( IMPORTING ev_vbak = ls_vbak
                                                                        et_vbpa = lt_vbpavb ).
    ENDIF.

*   Iterate through each role to obtain "customer" number, country, language and email address of partner
    LOOP AT ct_role_data ASSIGNING FIELD-SYMBOL(<ls_role_data>).
*      CLEAR: lv_pernr, lv_adrnr.
      CLEAR: ls_comm_data, lv_language.

      IF lt_vbpavb IS NOT INITIAL.
        READ TABLE lt_vbpavb ASSIGNING FIELD-SYMBOL(<ls_vbpavb>) WITH KEY vbeln = ls_vbak-vbeln
                                                                      parvw = <ls_role_data>-role.
        IF sy-subrc = 0.
          MOVE-CORRESPONDING <ls_vbpavb> TO ls_vbpa.
        ENDIF.
      ELSE.
*       Get address and business partner identifiers
        SELECT SINGLE addressid, customer, personnel, contactperson, partnerfunction, sddocpartneraddressreftype, addresspersonid, addressobjecttype
                 FROM i_sddocumentcompletepartners
                 INTO ( @ls_vbpa-adrnr, @ls_vbpa-kunnr, @ls_vbpa-pernr, @ls_vbpa-parnr, @ls_vbpa-parvw, @ls_vbpa-adrda, @ls_vbpa-adrnp, @ls_vbpa-addr_type )
                WHERE sddocument   = @<ls_role_data>-appl_object_id
                  AND partnerfunction = @<ls_role_data>-role.
      ENDIF.

      CHECK sy-subrc = 0.

      cl_sd_sls_fdp_v1_util=>so_instance->get_sales_org_data(
        EXPORTING
          iv_salesdocument = CONV #( <ls_role_data>-appl_object_id )
        IMPORTING
          ev_country_code  = <ls_role_data>-country
          ev_languge       = lv_language ).

      IF <ls_role_data>-language IS INITIAL.
        <ls_role_data>-language = lv_language.
      ENDIF.

*     Get type of partner number
      SELECT SINGLE nrart FROM tpar INTO @DATA(lv_nrart) WHERE parvw = @<ls_role_data>-role.
      CHECK sy-subrc = 0.

      CASE lv_nrart.
        WHEN if_sd_partner=>co_partner_type_code-employee.
          CHECK ls_vbpa-pernr IS NOT INITIAL.
          <ls_role_data>-id = ls_vbpa-pernr.

          cl_sd_sls_fdp_v1_util=>so_instance->get_employee_data(
            EXPORTING
              iv_pernr                 = ls_vbpa-pernr
              iv_parvw                 = <ls_role_data>-role
              iv_sales_document_number = CONV #( <ls_role_data>-appl_object_id )
            IMPORTING
              ev_comm_data             = ls_comm_data
              ev_language              = lv_language ).

*         We store 10 digit in OM for personal number instead of HR 8 digits
          CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
            EXPORTING
              input  = ls_vbpa-pernr
            IMPORTING
              output = <ls_role_data>-id.

*          <ls_role_data>-id = ls_vbpa-pernr.
          <ls_role_data>-language  = lv_language.
          <ls_role_data>-email_uri = ls_comm_data-smtp_addr.

        WHEN if_sd_partner=>co_partner_type_code-contact.
          <ls_role_data>-id = ls_vbpa-parnr.
          IF ls_vbpa-adrnr CA '$'.
            lv_addrhandle = ls_vbpa-adrnr.
            CLEAR ls_vbpa-adrnr.
          ELSE.
            CLEAR lv_addrhandle.
            TEST-SEAM contact_select_knvk.
              SELECT SINGLE parla FROM knvk INTO @DATA(lv_langu)
               WHERE parnr = @<ls_role_data>-id.
            END-TEST-SEAM.
            IF sy-subrc = 0 AND lv_langu IS NOT INITIAL.
              <ls_role_data>-language = lv_langu.
            ENDIF.
          ENDIF.

          CLEAR ls_address_value.
          TEST-SEAM contact_addr_get.
            CALL FUNCTION 'ADDR_GET'
              EXPORTING
                address_selection = VALUE addr1_sel( addrnumber = ls_vbpa-adrnr
                                                     addrhandle = lv_addrhandle )
              IMPORTING
                address_value     = ls_address_value
              EXCEPTIONS
                OTHERS            = 4.
          END-TEST-SEAM.
          CHECK sy-subrc = 0.
          IF ls_address_value-country IS NOT INITIAL.
            <ls_role_data>-country = ls_address_value-country.
          ENDIF.

          IF ls_address_value-addr_group = 'SD01' AND ls_address_value-langu IS NOT INITIAL.
            <ls_role_data>-language = ls_address_value-langu.
          ENDIF.

          cl_sd_sls_fdp_v1_util=>so_instance->get_contact_person_data(
            EXPORTING
              iv_parvw             = <ls_role_data>-role
              iv_kunnr_partner_num = ls_vbpa-kunnr
              iv_addressid         = ls_vbpa-adrnr
              iv_parnr_partner_num = ls_vbpa-parnr
              iv_language          = <ls_role_data>-language
            IMPORTING
              es_comm_data         = ls_comm_data ).
          <ls_role_data>-email_uri = ls_comm_data-smtp_addr.

        WHEN if_sd_partner=>co_partner_type_code-customer.
          <ls_role_data>-id = ls_vbpa-kunnr.
          IF ls_vbpa-adrnr CA '$'.
            lv_addrhandle = ls_vbpa-adrnr.
            CLEAR ls_vbpa-adrnr.
          ELSE.
            CLEAR lv_addrhandle.
          ENDIF.

*         Get correspondence language of partner
          TEST-SEAM customer_but000_select.
            SELECT SINGLE langu_corr FROM but000 INTO @lv_lngcor WHERE partner = @<ls_role_data>-id.
          END-TEST-SEAM.
*         Get country code and language associated with address id of partner
          IF ls_vbpa-addr_type IS INITIAL AND ls_vbpa-adrnr IS NOT INITIAL.
            IF ls_vbpa-adrda = 'E' OR ls_vbpa-adrda = 'F'.
              ls_vbpa-addr_type = '1'.
            ELSE.
              IF ls_vbpa-parnr IS NOT INITIAL.
                ls_vbpa-addr_type = '3'.
              ELSE.
                IF ls_vbpa-adrnp IS NOT INITIAL AND ls_vbpa-adrda = 'H'.
                  ls_vbpa-addr_type = '2'.
                ELSE.
                  ls_vbpa-addr_type = '1'.
                ENDIF.
              ENDIF.
            ENDIF.
          ENDIF.

          CLEAR ls_address_value.
          TEST-SEAM customer_addr_get.
            CALL FUNCTION 'VIEW_VBADR'
              EXPORTING
                input      = ls_vbpa
                langu_prop = <ls_role_data>-language
              IMPORTING
                adresse    = ls_vbadr
              EXCEPTIONS
                error      = 0.
          END-TEST-SEAM.

          <ls_role_data>-country = ls_vbadr-land1.

*         Set language depending on type of business partner
          IF lv_lngcor IS NOT INITIAL.
            <ls_role_data>-language = lv_lngcor.
          ELSEIF ls_vbadr-spras IS NOT INITIAL.
            <ls_role_data>-language = ls_vbadr-spras.
          ENDIF.

*         Get email address associated address id of partner
          TEST-SEAM customer_addr_comm_get.
            CALL FUNCTION 'ADDR_COMM_GET'
              EXPORTING
                address_handle = lv_addrhandle
                address_number = ls_vbpa-adrnr
                language       = <ls_role_data>-language
                table_type     = 'ADSMTP'
              TABLES
                comm_table     = lt_adsmtp
              EXCEPTIONS
                OTHERS         = 0.
          END-TEST-SEAM.
          READ TABLE lt_adsmtp REFERENCE INTO DATA(lr_adsmtp) INDEX 1.
          IF sy-subrc = 0.
            <ls_role_data>-email_uri = lr_adsmtp->smtp_addr.
          ENDIF.
      ENDCASE.
    ENDLOOP.
  ENDMETHOD.


  method IF_APOC_COMMON_API~GET_DATA_FOR_SENDER.

    data: lv_vbeln  type vbeln,
          lv_vkorg  type vkorg,
          lv_bukrs  type tvko-bukrs,
          lv_spras  type t001-spras,
          lv_adrnr  type adrnr,
          lt_adsmtp type table of adsmtp.

    " iterate through list of senders and populate data accordingly...
    loop at ct_sender_data reference into data(lr_sender_data).

      lv_vbeln = lr_sender_data->appl_object_id.

      cl_sd_sls_fdp_v1_util=>so_instance->get_sales_org_data(
        exporting
          iv_salesdocument = lv_vbeln
        importing
          ev_sales_org     = lv_vkorg
          ev_company_code  = lv_bukrs
          ev_address_id    = lv_adrnr
          ev_country_code  = lr_sender_data->country_code
          ev_languge       = lv_spras
      ).

      lr_sender_data->organization_id   = lv_bukrs.
      lr_sender_data->organization_type = 'COMPANY'.
      lr_sender_data->org_unit_id       = lv_vkorg.
      lr_sender_data->org_unit_type     = 'VKORG'.

      " get the email address associated address
      call function 'ADDR_COMM_GET'
        exporting
          address_number = lv_adrnr
          language       = lv_spras
          table_type     = 'ADSMTP'
        tables
          comm_table     = lt_adsmtp
        exceptions
          others         = 0.

      read table lt_adsmtp reference into data(lr_adsmtp) index 1.

      if ( sy-subrc is initial ).
        lr_sender_data->email_uri = lr_adsmtp->smtp_addr.
      endif.

    endloop.

  endmethod.


  method IF_APOC_COMMON_API~GET_EMAIL_TEMPLATE_CDS_KEYS.

    data: ls_cds_key type if_apoc_common_api=>ty_gs_cds_key.

    ls_cds_key-name   = 'SalesDocument'.
    ls_cds_key-value  = is_application_object-id.

    insert ls_cds_key into table et_cds_key.

  endmethod.


  METHOD IF_APOC_COMMON_API~GET_FDP_PARAMETER.

    DATA lt_root_keys TYPE if_apoc_or_h_api=>ty_gt_root_keys.
    DATA ls_root_keys TYPE if_apoc_or_h_api=>ty_gs_root_keys.
    DATA lt_item_data TYPE apoc_t_or_item.
    DATA wa_item_data TYPE apoc_s_or_item.
    DATA lo_srv_mgr    TYPE REF TO  /bobf/if_tra_service_manager.
    DATA: ls_key           TYPE if_apoc_common_api=>ty_gs_fdp_key,
          lt_key           TYPE if_apoc_common_api=>ty_gt_fdp_key,
          ls_fdp_parameter TYPE if_apoc_common_api=>ty_gs_fdp_parameter.
    DATA  lv_idx      TYPE i VALUE 0.

    CLEAR et_fdp_parameter.
    CLEAR lt_item_data.

    IF mo_apoc_or_h_api IS NOT BOUND.
      DATA(lo_apoc_or_h_api) = cl_apoc_or_h_factory=>get_helper_factory( )->get_apoc_or_api( ).
    ELSE.
      lo_apoc_or_h_api = mo_apoc_or_h_api.
    ENDIF.

    lo_srv_mgr ?= /bobf/cl_tra_serv_mgr_factory=>get_service_manager( iv_bo_key = if_apoc_output_request_c=>sc_bo_key ).

    DATA(lo_sls_data_ref) = cl_sls_data_ref=>get_instance( ).
    LOOP AT it_output_request_info REFERENCE INTO DATA(lr_output_request_info).

      " initialize output item root and detail
      IF ( lv_idx = 0 ).
        CLEAR lt_root_keys.
        ls_root_keys-appl_object_type = gc_appl_object_type.
        ls_root_keys-appl_object_id   = lr_output_request_info->appl_object_id.
        INSERT ls_root_keys INTO TABLE lt_root_keys.

        " retrieve output request for sales document
        lo_apoc_or_h_api->retrieve_output_request(
          EXPORTING
            io_srv_mgr        = lo_srv_mgr
            iv_retrieve_items = abap_true
            iv_fill_data      = abap_true
          IMPORTING
*           et_or_root_data   = lt_root_data
            et_or_item_data   = lt_item_data
          CHANGING
            ct_or_root_keys   = lt_root_keys ).
      ENDIF.

      "increment for the next loop iteration
      lv_idx = lv_idx + 1.

      CLEAR wa_item_data.

      READ TABLE lt_item_data INTO wa_item_data WITH KEY key = lr_output_request_info->output_item_key.

      CLEAR: lt_key, ls_key, ls_fdp_parameter.

      " pass parameters to the Form Data Provider (Language is taken care of for us...) so
      " that we can retrieve the data for the form

      CASE lr_output_request_info->fdp_srvc_name.

        WHEN 'FDP_V1_ORDER_CONFIRM_SRV' OR 'ZSALES_ORDER_CONFORMATION_SRV' .

          ls_key-name  = 'SalesOrder'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = lr_output_request_info->change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = lr_output_request_info->output_item_key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

*          set_transient_data(
*            io_sls_data_ref        = lo_sls_data_ref
*            ir_output_request_info = lr_output_request_info ).

        WHEN 'SD_SLS_FDP_V1_INQUIRY_SRV'.

          ls_key-name  = 'SalesDocument'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = wa_item_data-change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = wa_item_data-key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN 'SD_SLS_FDP_V1_QUOTATION_SRV'.

          ls_key-name  = 'SalesQuotation'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = wa_item_data-change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = wa_item_data-key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN 'SD_SLS_FDP_V1_CONTRACT_SRV'.

          ls_key-name  = 'SalesContract'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = wa_item_data-change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = wa_item_data-key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN 'SD_SLS_FDP_V1_RETURNS_SRV'.

          ls_key-name  = 'SalesReturns'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = wa_item_data-change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = wa_item_data-key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN 'SD_SLS_FDP_V1_ORDER_WO_CHARGE_SRV'.

          ls_key-name  = 'OrderWithoutCharge'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = wa_item_data-change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = wa_item_data-key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN 'SD_SLS_FDP_V1_DMR_SRV'.

          " this form data provider is not used any more....

          ls_key-name  = 'BillingDocument'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN 'SD_SLS_FDP_V1_DEBITMEMOREQ_SRV'.

          ls_key-name  = 'SalesDocument'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'ReceiverID'.
          ls_key-value = wa_item_data-receiver_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'ReceiverRole'.
          ls_key-value = wa_item_data-receiver_role.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = wa_item_data-change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = wa_item_data-key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN 'SD_SLS_FDP_V1_CREDITMEMOREQ_SRV'.

          ls_key-name  = 'SalesDocument'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'ReceiverID'.
          ls_key-value = wa_item_data-receiver_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'ReceiverRole'.
          ls_key-value = wa_item_data-receiver_role.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = wa_item_data-change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = wa_item_data-key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN 'SD_SLS_FDP_V1_SEPA_SRV'.

          " default naming convention used for the two query node arguments supplied to the customer's own FDP

          ls_key-name  = 'SalesDocument'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SEPAMandateUUID'.
          ls_key-value = ''.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

*          set_transient_data(
*            io_sls_data_ref        = lo_sls_data_ref
*            ir_output_request_info = lr_output_request_info ).

        WHEN 'SD_SLS_FDP_V1_SCHEDGAGRMT_SRV'.

          ls_key-name  = 'Salesschedulingagreement' ##NO_TEXT.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.

        WHEN OTHERS.

          " default naming convention used for the two query node arguments supplied to the customer's own FDP

          ls_key-name  = 'SalesDocument'.
          ls_key-value = lr_output_request_info->appl_object_id.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'SenderCountry'.
          ls_key-value = lr_output_request_info->sender_country.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'IsChangeDocument'.
          ls_key-value = lr_output_request_info->change_indicator.
          INSERT ls_key INTO TABLE lt_key.

          ls_key-name  = 'OutputRequestItemUUID'.
          ls_key-value = lr_output_request_info->output_item_key.
          INSERT ls_key INTO TABLE lt_key.

          ls_fdp_parameter-fdp_srvc_name = lr_output_request_info->fdp_srvc_name.
          ls_fdp_parameter-keys = lt_key.

          INSERT ls_fdp_parameter INTO TABLE et_fdp_parameter.
      ENDCASE.

      set_transient_data(
        io_sls_data_ref        = lo_sls_data_ref
        ir_output_request_info = lr_output_request_info ).
    ENDLOOP.
  ENDMETHOD.


  METHOD IF_APOC_COMMON_API~GET_LEGACY_DATA ##NEEDED.
    IF is_or_item-form_template_type <> if_apoc_or_c=>gc_form_type_adobe_form_xfa2.
      cl_sd_sls_om_factory=>get_nast_data_provider( )->set_output_process( if_sd_sls_om=>mc_om-output_process-business_rule_framework ).
    ENDIF.
  ENDMETHOD.


  method IF_APOC_COMMON_API~RENDER_DOCUMENT_LEGACY ##NEEDED.
  endmethod.


  method IF_APOC_OR_ITEM_VALIDATION~CHECK_ITEM.

*    IF ( is_or_item_data-output_type = cl_sd_sepa_om=>gc_sepa_output_type ).
*      cl_sd_sepa_om=>check_item( EXPORTING is_or_item = is_or_item_data io_message_collector = io_message_collector iv_severity = iv_standard_msg_severity ).
*    ENDIF.

*    IF is_or_item_data-output_type = if_om_doc_sepa_output=>gc_sepa_output_type.
    cl_om_doc_sepa_output_factory=>get_sepa_validator( )->check_or_item(
        exporting is_or_item = is_or_item_data
                  io_message_collector = io_message_collector ).
*    ENDIF.

  endmethod.


  method IF_APOC_OR_ITEM_VALIDATION~VALIDATE_CREATED_ITEM.

    data(lv_sepa_validated) = cl_om_doc_sepa_output_factory=>get_sepa_validator( )->validate_or_item(
        exporting is_or_item = is_or_item_data
        importing ev_validation_result = is_valid ).

    if lv_sepa_validated = abap_false.
      is_valid = cl_cdo_factory=>get_or_item_validator( gc_appl_object_type )->validate_or_item(
        exporting is_or_item = is_or_item_data ).
    endif.

  endmethod.


  method IF_SOMU_BADI_FORM_MASTER~GET_FORM_MASTER_DATA.

    data: ls_vbpa       type vbpa,
          ls_vbadr      type vbadr,
          ls_adrs       type adrs_print,
          lv_nrart      type nrart,
          lv_addrhandle type ad_handle.

    if cl_sd_sls_fdp_v1_util=>so_instance->has_transient_data( ).
      data(ls_partner) = cl_sd_sls_fdp_v1_util=>so_instance->get_transient_partner( iv_parvw = iv_recipient_role iv_language = iv_form_template_language ).
      ls_vbpa-vbeln = ls_partner-salesorder.
      ls_vbpa-adrnr = ls_partner-addressid.
      ls_vbpa-pernr = ls_partner-personnel.
      ls_vbpa-parvw = ls_partner-partnerfunction.
      ls_vbpa-kunnr = ls_partner-customer.
      ls_vbpa-adrda = ls_partner-sddocpartneraddressreftype.
      ls_vbpa-adrnp = ls_partner-addresspersonid.
      ls_vbpa-addr_type = ls_partner-addresstype.
      ls_vbpa-parnr = ls_partner-contactperson.
    else.
      select single sddocument as salesdocument, addressid, personnel, partnerfunction, customer, sddocpartneraddressreftype, addresspersonid, addressobjecttype, contactperson
               from i_sddocumentcompletepartners
              where ( sddocument = @iv_appl_object_id ) and ( partnerfunction = @iv_recipient_role )
               into @data(ls_prtnr).
      ls_vbpa-vbeln = ls_prtnr-salesdocument.
      ls_vbpa-adrnr = ls_prtnr-addressid.
      ls_vbpa-pernr = ls_prtnr-personnel.
      ls_vbpa-parvw = ls_prtnr-partnerfunction.
      ls_vbpa-kunnr = ls_prtnr-customer.
      ls_vbpa-adrda = ls_prtnr-sddocpartneraddressreftype.
      ls_vbpa-adrnp = ls_prtnr-addresspersonid.
      ls_vbpa-addr_type = ls_prtnr-addressobjecttype.
      ls_vbpa-parnr = ls_prtnr-contactperson.
    endif.

    select single nrart from tpar into (@lv_nrart)
          where ( parvw = @ls_vbpa-parvw ).

    if ( lv_nrart ne 'PE' ).
      if ls_vbpa-addr_type is initial and ls_vbpa-adrnr is not initial.
        if ls_vbpa-adrda = 'E' or ls_vbpa-adrda = 'F'.
          ls_vbpa-addr_type = '1'.
        else.
          if ls_vbpa-parnr is not initial.
            ls_vbpa-addr_type = '3'.
          else.
            if ls_vbpa-adrnp is not initial and ls_vbpa-adrda = 'H'.
              ls_vbpa-addr_type = '2'.
            else.
              ls_vbpa-addr_type = '1'.
            endif.
          endif.
        endif.
      endif.
      " get address structure for the partner containing which address type can be used to obtain printable address: personal vs business
      call function 'VIEW_VBADR'
        exporting
          input      = ls_vbpa
          langu_prop = iv_form_template_language
        importing
          adresse    = ls_vbadr.

      if ( ls_vbadr-adrnr ca '$' ).
        lv_addrhandle = ls_vbadr-adrnr.
        clear ls_vbadr-adrnr.
      endif.

      " get printable version of the address information in the language of receiver/country of sender
      call function 'ADDRESS_INTO_PRINTFORM'
        exporting
          address_type        = ls_vbadr-address_type
          address_number      = ls_vbadr-adrnr
          address_handle      = lv_addrhandle
          person_number       = ls_vbadr-adrnp
          sender_country      = iv_sender_country
          number_of_lines     = 8
          street_has_priority = abap_false
        importing
          address_printform   = ls_adrs.

      " Check if the partner is blocked
      data(dpp_check) = cl_sd_sls_om_dpp_factory=>get_dpp_check( ls_vbpa-vbeln ).
      data(lv_is_blocked) = dpp_check->is_partner_blocked( iv_partner_role = iv_recipient_role iv_partner_number = ls_vbpa-kunnr iv_address_number = ls_vbadr-adrnr ).
      if ( lv_is_blocked = abap_true ).
        " Mask the address information
        call method dpp_check->mask_structure
          changing
            cs_structure = ls_adrs.
      endif.

      " map receiver address
      cs_form_master_data-receiver_address-address_id     = ls_vbadr-adrnr.
      cs_form_master_data-receiver_address-person_id      = ls_vbadr-adrnp.
      cs_form_master_data-receiver_address-address_type   = ls_vbadr-address_type.
      cs_form_master_data-receiver_address-address_line_1 = ls_adrs-line0.
      cs_form_master_data-receiver_address-address_line_2 = ls_adrs-line1.
      cs_form_master_data-receiver_address-address_line_3 = ls_adrs-line2.
      cs_form_master_data-receiver_address-address_line_4 = ls_adrs-line3.
      cs_form_master_data-receiver_address-address_line_5 = ls_adrs-line4.
      cs_form_master_data-receiver_address-address_line_6 = ls_adrs-line5.
      cs_form_master_data-receiver_address-address_line_7 = ls_adrs-line6.
      cs_form_master_data-receiver_address-address_line_8 = ls_adrs-line7.

      "Title gets set in the form, no action here    " DO NOTHING FOR NOW...
    else.
      "Employee address data support within the OData service
      clear cs_form_master_data-receiver_address.
    endif.
  endmethod.


  method SET_TRANSIENT_DATA.

    if  ir_output_request_info->preview_indicator = abap_true and "When the print is done from Job there cannot be transient data
        io_sls_data_ref is bound and  " Fiori in display mode SAPMV45A is not instantiated.
        cl_sd_rap_common_factory=>get_entry_context( )-is_sticky_ui = abap_true. "Only from Fiori we need the logic.

      io_sls_data_ref->get_vbak(
        importing
          ev_dataloss = data(lv_dataloss) ).

      if lv_dataloss = abap_true. "Only if data was changed in the session
        if ir_output_request_info->appl_object_id = if_sd_sls_om=>mc_om-temporary_document_key-document_number.
          data(lv_sales_doc_number) = if_sd_sdoc_behavior_util=>co_document_id_temp.
        else.
          lv_sales_doc_number = ir_output_request_info->appl_object_id.
        endif.
        so_sd_sls_om->set_transient_data( lv_sales_doc_number ).
      endif.

    endif.

  endmethod.
ENDCLASS.
*******************************************************************************************************
