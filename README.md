MYL
===\
index.jsp for Borrower


<%@page import="com.slma.utils.UpromiseCodeUtil"%>
<%@page import="com.slma.selfservice.core.tridion.TridionHelper"%>
<%@page import="com.slma.utils.Money"%>
<%@page import="com.slma.utils.ElectronicBillPaymentSubmitDateSorter"%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd"><html><!-- InstanceBegin template="/templates/main_Split_Hor_Vert.dwt" codeOutsideHTMLIsLocked="false" -->
<%String dwtName="slm_base_template.dwt"; %>
<%String headerName="header_main"; %>
<%@ taglib uri="/borrower/taglib/omniture.tld"  prefix="omniture"%>

<% 
  String txt;
	int pageType=0;
	//1=homepage,2=accountInformation,3=makeAPayment,4=changePayment,5=postponePayment,6=personalprofile,7=customerservice
	com.slma.framework.SLMSession slmSession = new com.slma.framework.SLMSession (request, response, true);
	String referrer = slmSession.getString("referrer");
	boolean referred = (slmSession.getString("referrer") != null && !slmSession.getString("referrer").equals("")) ? true : false;
	String globalResourceID = new com.slma.selfservice.core.LanguageManager().initJSP(slmSession, "global", "global");
	boolean needsToggle = false;
%>
<%@ taglib uri="/borrower/taglib/contentManager.tld" prefix="contentManager" %> 
 <%@ taglib uri="/borrower/taglib/jqueryPlugin.tld" prefix="jqueryPlugin" %>
   <head>
   <!-- InstanceBeginEditable name="doctitle" -->
<%String locationID =null; %>
<script language="JavaScript">
		<!--
		<%if ("InProgress".equals(slmSession
					.getString("omnitureTracking.omnitureLoginIssued"))) {%>
			 var s_events="event20";
		<%}
			boolean showSelectAccountLink = new Boolean(slmSession
					.getString("showSelectAccountLink")).booleanValue();
			boolean showPrimaryBorrowerLink = new Boolean(slmSession
					.getString("showPrimaryBorrowerLink")).booleanValue();
			int loanAdvisorWidth = 518;%> 
		//-->
	</script>
	<script type="text/javascript" src="/borrower/scripts/selfservice.js"></script>
<%@page import="java.util.*"%>	
<%@page import="com.slma.selfservice.borrower.payment.ElectronicBill" %>
<%@page import="java.text.*" %>
<% final DateFormat dateFormatter = new SimpleDateFormat("MM/dd/yyyy");
ArrayList<ElectronicBill> pendingAndScheduledBillsList = (ArrayList<ElectronicBill>)slmSession.get("response.pendingAndScheduledBillsList"); 
HashMap<String, Map<Date, Money>> recentDateAndPaymentPendingMapByConfirmationNumber = (HashMap<String, Map<Date, Money>>)slmSession.get("response.recentDateAndPaymentPendingMapByConfirmationNumber"); 
%>
<%
	needsToggle = true;
	pageType = 1;
	new com.slma.selfservice.core.LanguageManager()
			.setSessionContent(
					slmSession,
					"com.slma.selfservice.controller.resourceBundles.resources",
					"", "index");
	String resourceID = "response.rb.index";
%>
<title><%=slmSession.getString(resourceID  
							+ ".SallieMaeManageYourLoans")%></title>

<!--  SSNLast4=<%=slmSession.getString("response.Tealeaf.SSNLast4")%>,DOB=<%=slmSession.getString("response.Tealeaf.DOB")%> -->
<script language="JavaScript">
	<!--
	function popup(url)
	{
		document.all && (navigator.userAgent.indexOf("Mac") > -1)? wid = 462 : wid = 469;
		newwin=window.open(url,'SallieMae',"WIDTH=" + wid + ",HEIGHT=475,menubar=yes,scrollBars=yes,resizable=yes");
		newwin.focus();
	}
	
	function popup(url, height, width)
	{
		newwin=window.open(url,'SallieMae',"WIDTH=" + width + ",HEIGHT=" + height + ",menubar=yes,scrollBars=yes,resizable=yes");
		newwin.focus();
	}

	// if the cookie is not found, then this user (during this browser session, anyway) has not yet seen the survey
	// page, so we need to pop it up for the borrower
    <%String sPopupSurvey = (String) slmSession
					.get("survey.IsPopupSurvey");
			String sHasSeenSurvey = (String) slmSession
					.get("survey.HasSeenSurvey");
			String sSurveyHeight = (String) slmSession
					.get("survey.WindowHeight");
			String sSurveyWidth = (String) slmSession.get("survey.WindowWidth");

			// We don't want the survey to keep popping up to the user
			if (sHasSeenSurvey == null) {
				slmSession.put("survey.HasSeenSurvey", "true");
				if (sPopupSurvey != null
						&& sPopupSurvey.equalsIgnoreCase("true")) {%>
				popup('<%=request.getServletPath()%>?StateName=survey', <%=sSurveyHeight%>, <%=sSurveyWidth%>);
			<%}
			}%>
	// -->
	</script>

<%@ include file="/borrower/includes/tealeafScripts.jsp"%>



<!-- InstanceEndEditable -->
   <%if(slmSession.isDepartmentOfEd()){%>
   <%@ include file="/borrower/includes/edGovScripts.jsp" %>
   <%}else{%>
   <%@ include file="/borrower/includes/mylScripts.jsp" %>
   <%}
%>
  
   </head>

    <body>
       <div id="container"> 
	     <form action="<% txt = (String) slmSession.get("response.PostAction"); if (null != txt) out.print(txt); %>" method="POST" name=            "mainform">
		<input type="hidden" name="Select" value="<% txt = (String) slmSession.get("response.Select"); if (null != txt) out.print(txt); %>" />
		<input type="hidden" name="StateID" value="<% txt = (String) slmSession.get("response.StateID"); if (null != txt) out.print(txt); %>" />
		<input type="hidden" name="StateName" value="">
		<!-- InstanceBeginEditable name="dev" -->
<input type="hidden" name="Key" value=""> <!-- InstanceEndEditable -->		
    </form>
			
    <table border="0" width="766"> <!--Main Table-->
	       <tr>	<!-- Row 1:Header Row Begins -->
		       <td><!-- #BeginLibraryItem "/Library/header_main.lbi" --><%if(slmSession.isDepartmentOfEd()){%>
	<%@ include file="/borrower/includes/edGovHeader.jsp" %> 
<%}else{%>
	<%@ include file="/borrower/includes/mylHeader.jsp" %>
<%}%>
<!-- #EndLibraryItem --></td>
		   </tr> <!--Row 1: Header Row ends-->
		   
		    <tr>	<!-- Row 2:BreadCrumb/Page Title/Search Box Row Begins -->
		     <td>
		         <table width="766" border="0" cellpadding="0" cellspacing="0"><!--Table for BreadCrumb/Page Title/Search Box Begins-->
			         <tr> 
				        <td width="466"> <!--Table Data value for BreadCrumb and Pagetitle begins-->
					         <table width="100%" border="0" cellpadding="0" cellspacing="0"> <!--Table for BreadCrumb and PageTitle Begins-->
						        <tr> <!--BreadCrumb row begins-->
							      <td class="textblue" colspan="2"><br>	
							        <!-- Bread Crumbs -->
									    	<!-- InstanceBeginEditable name="bread_crumbs" --> Account Summary
						<!-- InstanceEndEditable -->
	  						      </td>
					           </tr> <!--BreadCrumb row begins-->
						
						       								 										 
								 <tr> <!--PageTitle row begins-->
							        <td align="center" class="textheader"><!-- Page Title -->
						    	        <!-- InstanceBeginEditable name="page_title" --> 
						    	        <contentManager:PrimaryBorrower>
											<%=slmSession.getString(resourceID + ".YourAccountSummary")%>
										</contentManager:PrimaryBorrower> <contentManager:Cosigner>
											<%=slmSession.getString(resourceID + ".CosignerAccountSummary")%>
										</contentManager:Cosigner> <contentManager:Endorser>
											<%=slmSession.getString(resourceID + ".EndorserAccountSummary")%>
										</contentManager:Endorser>
						<!-- InstanceEndEditable -->							        
									</td>						
						         </tr><!--PageTitle row ends-->
			              
						  </table> <!--Table for BreadCrumb and PageTitle Ends-->			           
					   </td>  <!--Table Data value for BreadCrumb and Pagetitle Ends-->
				         
						  <td width="300" valign="bottom" align="left">	
						 <!-- TableData value for Search box -->
					        <form action="<%= slmSession.getString("response.PostAction") %>" method="POST" name=	"searchform">
					        <input type="hidden" name="Select" value="global_search" />
					        <input type="hidden" name="StateID" value="<%= slmSession.getString("response.StateID") %>" />
					        <input type="hidden" name="StateName" value="">
					        <br/>
							<input type="text" name="query" id="Search field">
					        <label for="Search field">
							<a href="javascript:document.searchform.submit();">
							<img src="/borrower/images/bt_search.gif" width="60" height="20" border="0" align="absbottom" alt="search"></a><br/></label>
		                   </form>			          
						  </td>
					 </tr>
			   </table>
		         <!--Table for BreadCrumb/Page Title/Search Box Ends-->
		     </td>
      </tr>  <!-- Row 2:BreadCrumb/Page Title/Search Box Row Ends -->
	  <%@ include file="/borrower/includes/errormessages.jsp" %> 
			 <tr><td>&nbsp; </td></tr>
			 <tr> <!--Row3: Body Row-->
			  <td colspan="2">
			   	  
				  <table border="0" width="766">
				
					<tr>
						<td valign="top" width="466"> <!-- Left column -->
					
							<table border="0" width="100%" cellpadding="0" cellspacing="0">
							
								<tr>	<!-- Top Left Body Content goes here -->
							       <td>
						    	     <!-- InstanceBeginEditable name="top_left_body" --> <!--  Start Your Account Summary Section -->
						   <%--  	  
						<!-- Modal Window Start-->
						<contentManager:AnyTwoPlatforms>
							<%@page import="com.slma.selfservice.controller.borrower.PlatformUtilityController "%>	
							<%if(slmSession.getString(PlatformUtilityController.SESSION_LABEL_MODAL_SHOWN).equals("")||slmSession.getString(PlatformUtilityController.SESSION_LABEL_MODAL_SHOWN).equals("false")){ %>
					 			<%@ include file="/borrower/includes/enhancedLoginModalPage.jsp" %>
								<%slmSession.put(PlatformUtilityController.SESSION_LABEL_MODAL_SHOWN, "true"); %>
							<%} %>
							
						</contentManager:AnyTwoPlatforms>
						<!-- Modal Window End -->
						--%>
						<%-- CrossSellAd:2010-10 --%>
						<contentManager:SallieMae>
					 	<!--  <ABTest>B </ABTest> -->
						 	<%@page import="com.slma.selfservice.controller.borrower.PlatformUtilityController "%>	
						 	<%@ page import="com.slma.selfservice.borrower.core.CrossSellAd" %>
						 	<% locationID = "MYL003"; %>
							<%CrossSellAd crossSellLightbox = (CrossSellAd)slmSession.get("response.crossSellAd." + locationID);%>
							<%if(crossSellLightbox!=null && com.slma.utils.StringFunctions.isNotBlank(crossSellLightbox.getAdSnippet())){%>
					 	
						 		<script type='text/javascript' >
						 			var lightBoxAdSnippet = "<div style=\"display:none\"><div class=\"crosssell-content\"><form action=\"#\" style=\"display:none\"><div class=\"crosssell-actionlink\"><!-- used for jquery submission --></div><%=com.slma.utils.StringFunctions.convJS(crossSellLightbox.getAdSnippet())%></form></div></div>";
					 			</script>
					 			
					 			<link type='text/css' href='/borrower/css/jquery/printCrossSellLightbox.css' rel='stylesheet' media='screen' />
					 			<script type='text/javascript' src='/borrower/scripts/jquery/printCrossSellLightbox.js'></script>
					  			<div id='content'>
									<div id='crosssell-form'>
										<div style="display:none;">
											<input type='button' id ='crosssellbtn' name='crosssellbtn' value='Modal' class='crosssell'/>
										</div>
									</div>
								</div>
					  			<%} %>
						</contentManager:SallieMae>

						
						
							<table width="97%" border="0" cellspacing="0" cellpadding="0">
							
								 <tr>
									<td width="100%" colspan="2" height="1" class="straight">&nbsp;</td>
								</tr>
								<tr>
									<td width="5%"><img border=0 alt=""
										src="/borrower/images/spacer.gif"></td>
									<td width="95%" valign="top">
									
									<contentManager:NotFDRFlow>
									
										<table border="0" cellpadding="3" cellspacing="0" width="100%">
											<tr>
												<td width="5%">&nbsp;</td>
												<td align="Left" valign="middle" width="45%" >
													<contentManager:PrimaryBorrower>
														<b><%=slmSession.getString(resourceID + ".YourAccountNumber")%>:</b>
													</contentManager:PrimaryBorrower>
													<contentManager:NotPrimaryBorrower>
														<b><%=slmSession.getString(resourceID + ".AccountFor")%>&nbsp;<%=slmSession.getHtml(slmSession.get("response.Name"))%>:</b>
													</contentManager:NotPrimaryBorrower>
													<jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%=TridionHelper.getToolTip("Account Number")%>' />
												</td>
												<td width="5%">&nbsp;&nbsp;</td>
												<td width="15%" align="Left" valign="middle">
													<!--CIN-Start--><%=slmSession.getHtml(slmSession.get("response.cin"))%><!--CIN-End-->
												</td>
												<td width="5%">&nbsp;&nbsp;</td>
												<td width="25%">&nbsp;&nbsp;</td>
											</tr>
											
										 <tr class="spreadsheetrule" >
												<td width="5%" >&nbsp;</td>
												<td colspan="3"" align="Left" valign="middle">
												
													<div id="totalAmountSection" style="display:none">
            <table border="0" cellpadding="3" cellspacing="0" width="100%">             
           <tr class="bggray">
                <td colspan="3" ><%=slmSession.getString(resourceID + ".OriginalPrincipalBalance") %></td>
                <td align="right"><%=slmSession.getHtml(slmSession.get("response.originalPrincipalBalance"))%></td>
             </tr>
         
            <tr class="bggray">
               <td colspan="3"><%=slmSession.getString(resourceID + ".CapitalizedInterest")%></td>
               <td align="right"><%=slmSession.getHtml(slmSession.get("response.capitalizedInterest"))%></td>
              </tr>
              
              <tr>
        <td colspan="3"><img src="/borrower/images/spacer.gif" width=30" height="10" border="0" alt=""></td>
            </tr>
         
               <tr class="bggray">
        <td colspan="3"><%=slmSession.getString(resourceID +".OutstandingPrincipalBalance") %></td>
        <td align="right"><%=slmSession.getHtml(slmSession.get("response.outstandingPrincipalBalance"))%></td>
   </tr>
 
   <tr class="bggray">
        <td colspan="3"><%=slmSession.getString(resourceID + ".AccruedInterest") %></td>
        <td align="right"><%=slmSession.getHtml(slmSession.get("response.accruedInterest"))%></td>
   </tr>
        
                <tr class="bggray">
                <td colspan="3"><img src="/borrower/images/spacer.gif" width="1" height="1" border="0" alt=""></td>
                <td align="right"><img src="/borrower/images/spacer_black.gif" width="60" height="1" border="0" alt=""></td>
              </tr>
             </table></div></td>
												
	                          <td width="5%">&nbsp;</td>
												<td width="25%">&nbsp;</td>
												</tr>
								
								     <tr class="spreadsheetrule" >
                                              <td width="5%" >&nbsp;</td>
												<td width="45%" align="Left" valign="middle" >
											     <b><%=slmSession.getString(resourceID + ".TotalAmountOwed")%>:</b>
													<jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%=TridionHelper.getToolTip("Total Amount Outstanding")%>' />
												</td>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="15%" align="right" valign="middle" >
													<%=slmSession.getHtml(slmSession.get("response.totalAmountOutstanding"))%>
												</td>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="25%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
											</tr>
																							
											
												<tr class="spreadsheetrule">
											<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td colspan="5" class="smalltext">
											       <a href="javascript:void(0);" onclick="showSection('totalAmountSection');hideSection('showTotalAmountSectionLink');showSection('hideTotalAmountSectionLink')" id="showTotalAmountSectionLink"><%=slmSession.getString(resourceID + ".ShowDetailLink") %></a>
												   <a href="javascript:void(0);" onclick="hideSection('totalAmountSection');hideSection('hideTotalAmountSectionLink');showSection('showTotalAmountSectionLink')" id="hideTotalAmountSectionLink" style="display:none"><%=slmSession.getString(resourceID + ".HideDetailLink") %></a>
                                                  </td>
                                           </tr>
                                    
											
											<tr>
												<td width="5%">&nbsp;</td>
												<td width="45%" align="Left" valign="middle">
													<b><%=slmSession.getString(resourceID + ".DueDate")%>:</b>
													<jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%=TridionHelper.getToolTip("Next Payment Due Date")%>' />
													
												</td>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="15%" align="right" valign="middle">
													<%=slmSession.getHtml(slmSession.get("response.dueDate"))%>
												</td>
												<td width="5%" ><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="25%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												
											</tr>
											
											
										
											<tr class="spreadsheetrule">
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td colspan="3"" align="Left" valign="middle" >
											 
											  <div id="paymentAmountDueSection" style="display:none">
            <table border="0" cellpadding="3" cellspacing="0" width="100%">             
           <tr class="bggray">
              <td colspan="3" ><%=slmSession.getString(resourceID+ ".PastDueAmount") %></td>
               <td align="right"><%=slmSession.getHtml(slmSession.get("response.pastDueAmount"))%></td>
             </tr>
         
            <tr class="bggray">
                <td colspan="3" ><%=slmSession.getString(resourceID+ ".LateFeeAndOtherCharges") %></td>
               <td align="right"><%=slmSession.getHtml(slmSession.get("response.lateFeeAndOtherCharges"))%></td>
             </tr>
         
                <tr class="bggray">
                 <td colspan="3"><%=slmSession.getString(resourceID+ ".PresentAmountDue") %></td>
                <td align="right"><%=slmSession.getHtml(slmSession.get("response.presentAmountDue"))%></td>
              </tr>
        
                <tr class="bggray">
                <td colspan="3"><img src="/borrower/images/spacer.gif" width="1" height="1" border="0" alt=""></td>
                <td align="right"><img src="/borrower/images/spacer_black.gif" width="60" height="1" border="0" alt=""></td>
              </tr>
             </table></div></td>
                                        <td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="25%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												</tr>
									
                						<tr class="spreadsheetrule">
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="45%" align="Left" class="spreadsheetrule" valign="middle">
													<b><%=slmSession.getString(resourceID + ".PaymentAmountDue")%>:</b>
													<jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%=TridionHelper.getToolTip("Pay This Amount")%>' />
												</td>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="15%" align="right" valign="middle">
												&nbsp;&nbsp;<%=slmSession.getHtml(slmSession.get("response.payThisAmount"))%>
												</td>
												<td width="5%" class="spreadsheetrule">&nbsp;</td>
												
												<td width="25%" valign="middle"><a href="javascript:Submit('menu_EBPP_Paynow')">
													<img src="/borrower/images/bt_pay_now.gif" border="0" alt="Pay Now"></a>
												</td>
											</tr>
								
									<tr class="spreadsheetrule">
											<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td colspan="5" class="smalltext">
												       <a href="javascript:void(0);"  onclick="showSection('paymentAmountDueSection');hideSection('showPaymentAmountDueSectionLink');showSection('hidePaymentAmountDueSectionLink')" id="showPaymentAmountDueSectionLink"><%=slmSession.getString(resourceID + ".ShowDetailLink") %></a>
													<a href="javascript:void(0);" onclick="hideSection('paymentAmountDueSection');hideSection('hidePaymentAmountDueSectionLink');showSection('showPaymentAmountDueSectionLink')"  id="hidePaymentAmountDueSectionLink" style="display:none"><%=slmSession.getString(resourceID + ".HideDetailLink") %></a>
                                                      </td>
                                                        </tr>
								
											<tr>
												<td colspan=6><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
											</tr>
										
<%
										if ( ("0".equals(slmSession.getString("response.activeBillGroupCount"))) || 
											 ( (Integer.valueOf(slmSession.getString("response.activeBillGroupCount")) > 0)
										       && ("false".equals(slmSession.get("response.hasRepaymentLoans")))) )
										{
%>

											<tr>
												
												<td colspan="100%"><%=slmSession.getString(resourceID + ".LoanDetailsLink1")%>
													
														<a href="javascript:SubmitMYL('/loanSummary')"><b><%=slmSession.getString(resourceID + ".LoanDetailsLink2")%></b></a>
													<%=slmSession.getString(resourceID + ".LoanDetailsLink3")%><br>
													<br>
												</td>
											</tr>
<% 
										}
%>	
<% 
										if( "true".equals(slmSession.get("response.hasRepaymentLoans")) )
										{
%>										
											<tr>
												<td valign="Left" colspan="6">
													<a href="javascript:SubmitMYL('/loanSummary')">
														<%=slmSession.getString(resourceID + ".ViewBillingAndLoanDetails")%></a>
												</td>
											</tr>
<% 
										}
%>
										</table>
									
									</contentManager:NotFDRFlow>
									
									
									<contentManager:FDRFlow>
									<%if( "true".equals(slmSession.get("EILSPLASHEX"))  ||  "true".equals(slmSession.get("response.EILPAYNOWFLOWSPLASHEX")) ){ %>
										<table border="0" cellpadding="3" cellspacing="0" width="100%">    
							               <tr>
										       <td>&nbsp;</td>
								       			<td>
						          				<font color="#ff0000"><br><%=slmSession.getString(resourceID + ".fdrLoanDataRefreshing") %></font>
										       </td>
									       </tr>
									     </table>
									<%}else if (slmSession.getBorrowerUsingMYL().getAccountProfile().getNumberOfFDRLoans() == 0){ %>
										<table border="0" cellpadding="3" cellspacing="0" width="100%">
											<tr>
												<td>&nbsp;</td>
								       			<td><br><%=slmSession.getString(resourceID + ".fdrLoanDataNotAvailable") %></td>
									    	</tr>
									    </table>
								     <%}else{ %>
										<table border="0" cellpadding="3" cellspacing="0" width="100%">
											<tr>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td align="Left" valign="middle" width="45%" >
													<contentManager:PrimaryBorrower>
														<b><%=slmSession.getString(resourceID + ".YourAccountNumber")%>:</b>
													</contentManager:PrimaryBorrower>
													<contentManager:NotPrimaryBorrower>
														<b><%=slmSession.getString(resourceID + ".AccountFor")%>&nbsp;<%=slmSession.getHtml(slmSession.get("response.Name"))%>:</b>
													</contentManager:NotPrimaryBorrower>
													<jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%=TridionHelper.getToolTip("Account Number")%>' />
												</td>
												<td width="5%">&nbsp;&nbsp;</td>
												<td width="15%" align="Left" valign="middle">
													<!--CIN-Start--><%=slmSession.getHtml(slmSession.get("response.cin"))%><!--CIN-End-->
												</td>
												<td width="5%">&nbsp;&nbsp;</td>
												<td width="25%">&nbsp;&nbsp;</td>
											</tr>
											
										 <tr class="spreadsheetrule" >
												<td width="5%" >&nbsp;</td>
												<td colspan="3"" align="Left" valign="middle">
												
													<div id="totalAmountSectionFDR" style="display:none">
            <table border="0" cellpadding="3" cellspacing="0" width="100%">             
           <tr class="bggray">
                <td colspan="3" ><%=slmSession.getString(resourceID + ".OriginalPrincipalBalance") %></td>
                <td align="right"><%=slmSession.getHtml(slmSession.get("response.originalPrincipalBalance"))%></td>
             </tr>
         
            <tr class="bggray">
               <td colspan="3"><%=slmSession.getString(resourceID + ".CapitalizedInterest")%></td>
               <td align="right"><%=slmSession.getHtml(slmSession.get("response.capitalizedInterest"))%></td>
              </tr>
              
              <tr>
        <td colspan="3"><img src="/borrower/images/spacer.gif" width=30" height="10" border="0" alt=""></td>
            </tr>
         
               <tr class="bggray">
        <td colspan="3"><%=slmSession.getString(resourceID +".OutstandingPrincipalBalance") %></td>
        <td align="right"><%=slmSession.getHtml(slmSession.get("response.outstandingPrincipalBalance"))%></td>
   </tr>
 
   <tr class="bggray">
        <td colspan="3"><%=slmSession.getString(resourceID + ".AccruedInterest") %></td>
        <td align="right"><%=slmSession.getHtml(slmSession.get("response.accruedInterest"))%></td>
   </tr>
        
                <tr class="bggray">
                <td colspan="3"><img src="/borrower/images/spacer.gif" width="1" height="1" border="0" alt=""></td>
                <td align="right"><img src="/borrower/images/spacer_black.gif" width="60" height="1" border="0" alt=""></td>
              </tr>
             </table></div></td>
												
	                          <td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="25%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												</tr>
								
								     <tr class="spreadsheetrule" >
                                              <td width="5%" >&nbsp;</td>
												<td width="45%" align="Left" valign="middle" >
											     <b><%=slmSession.getString(resourceID + ".TotalAmountOwed")%>:</b>
													<jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%=TridionHelper.getToolTip("Total Amount Outstanding")%>' />
												</td>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="15%" align="right" valign="middle" >
													<%=slmSession.getHtml(slmSession.get("response.totalAmountOutstanding"))%>
												</td>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="25%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
											</tr>
																							
											
												<tr class="spreadsheetrule">
											<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td colspan="5" class="smalltext">
											       <a href="javascript:void(0);" onclick="showSection('totalAmountSectionFDR');hideSection('showTotalAmountSectionLinkFDR');showSection('hideTotalAmountSectionLinkFDR')" id="showTotalAmountSectionLinkFDR"><%=slmSession.getString(resourceID + ".ShowDetailLink") %></a>
												   <a href="javascript:void(0);" onclick="hideSection('totalAmountSectionFDR');hideSection('hideTotalAmountSectionLinkFDR');showSection('showTotalAmountSectionLinkFDR')" id="hideTotalAmountSectionLinkFDR" style="display:none"><%=slmSession.getString(resourceID + ".HideDetailLink") %></a>
                                                  </td>
                                           </tr>
                                    
											
											<tr>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="45%" align="Left" valign="middle">
													<b><%=slmSession.getString(resourceID + ".DueDate")%>:</b>
													<jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%=TridionHelper.getToolTip("Next Payment Due Date")%>' />
												</td>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="15%" align="right" valign="middle">
													<%=slmSession.getHtml(slmSession.get("response.dueDate"))%>
												</td>
												<td width="5%" ><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="25%" ><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												
											</tr>
											
											
										
											<tr class="spreadsheetrule">
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td colspan="3"" align="Left" valign="middle" >
											 
											  <div id="paymentAmountDueSectionFDR" style="display:none">
            <table border="0" cellpadding="3" cellspacing="0" width="100%">             
           <tr class="bggray">
              <td colspan="3" ><%=slmSession.getString(resourceID+ ".PastDueAmount") %></td>
               <td align="right"><%=slmSession.getHtml(slmSession.get("response.pastDueAmount"))%></td>
             </tr>
         
            <tr class="bggray">
                <td colspan="3" ><%=slmSession.getString(resourceID+ ".LateFeeAndOtherCharges") %></td>
               <td align="right"><%=slmSession.getHtml(slmSession.get("response.lateFeeAndOtherCharges"))%></td>
             </tr>
         
                <tr class="bggray">
                 <td colspan="3"><%=slmSession.getString(resourceID+ ".PresentAmountDue") %></td>
                <td align="right"><%=slmSession.getHtml(slmSession.get("response.presentAmountDue"))%></td>
              </tr>
        
                <tr class="bggray">
                <td colspan="3"><img src="/borrower/images/spacer.gif" width="1" height="1" border="0" alt=""></td>
                <td align="right"><img src="/borrower/images/spacer_black.gif" width="60" height="1" border="0" alt=""></td>
              </tr>
             </table></div></td>
                                        <td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="25%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												</tr>
									
                						<tr class="spreadsheetrule">
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="45%" align="Left" class="spreadsheetrule" valign="middle">
													<b><%=slmSession.getString(resourceID + ".PaymentAmountDue")%>:</b>
													<jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%=TridionHelper.getToolTip("Pay This Amount")%>' />
												</td>
												<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td width="15%" align="right" valign="middle">
												<img border=0 alt="" src="/borrower/images/spacer.gif"><%=slmSession.getHtml(slmSession.get("response.payThisAmount"))%>
												</td>
												<td width="5%" class="spreadsheetrule"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												
												<td width="25%" valign="middle"><a href="javascript:Submit('global_FDRPayment')">
													<img src="/borrower/images/bt_pay_now.gif" border="0" alt="Pay Now"></a>
												</td>
											</tr>
								
									<tr class="spreadsheetrule">
											<td width="5%"><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
												<td colspan="5" class="smalltext">
												       <a href="javascript:void(0);"  onclick="showSection('paymentAmountDueSectionFDR');hideSection('showPaymentAmountDueSectionLinkFDR');showSection('hidePaymentAmountDueSectionLinkFDR')" id="showPaymentAmountDueSectionLinkFDR"><%=slmSession.getString(resourceID + ".ShowDetailLink") %></a>
													<a href="javascript:void(0);" onclick="hideSection('paymentAmountDueSectionFDR');hideSection('hidePaymentAmountDueSectionLinkFDR');showSection('showPaymentAmountDueSectionLinkFDR')"  id="hidePaymentAmountDueSectionLinkFDR" style="display:none"><%=slmSession.getString(resourceID + ".HideDetailLink") %></a>
                                                      </td>
                                                        </tr>
								
	                                       <tr>
												<td colspan=6><img border=0 alt="" src="/borrower/images/spacer.gif"></td>
											</tr>
											<tr>
												<td valign="Left" colspan="6">
														<a href="javascript:SubmitMYL('/loanSummary')">
															<%=slmSession.getString(resourceID + ".ViewBillingAndLoanDetails")%></a>
												</td>
											</tr>
										</table>
									<%} %>
									</contentManager:FDRFlow>
									
									
									
									</td>
								</tr>
							</table>
							<br>
						<!--  End Your Account Summary Section --> <!-- InstanceEndEditable -->							       										
								   </td>
						        </tr>
								
								<tr>	<!-- Bottom Left Body Content goes here -->
								   <td width="100%">
						    	   		<!-- InstanceBeginEditable name="bottom_left_body" -->
						    	   		
						    	   		
											<contentManager:SallieMae>
							                 	<%-- CrossSellAd:2011-02 --%>
											 	<% locationID = "MYL012"; %>
											 	<!--  <ABTest>B </ABTest> -->
											  	<%@include file="/borrower/includes/printCrossSellAds.jsp" %>
											  <br><br>
											</contentManager:SallieMae>
										
						    	   		
								<!--  Start Important Account Information --> 							
								<table border="0" cellspacing="0" cellpadding="0" width="97%">
									<tr class="bgglossarybox">
										<td width="2%">
											<img width="2%" border=0 alt="" src="/borrower/images/spacer.gif">
										</td>
										<td width="98%" colspan="2">
											<%=slmSession.getString(resourceID + ".ImportantAccountInformation")%>
										</td>
									</tr>
									<tr>
										<td colspan="2"><img height="5" border=0 alt="" src="/borrower/images/spacer.gif"></td>
									</tr>
									<tr>
										<td width="1%"><img width="5%" border=0 alt="" src="/borrower/images/spacer.gif"></td>
										<td width="99%" valign="top">
										<table border="0" cellpadding="3" cellspacing="0" width="100%">
											<tr>
												<td colspan="2" height="1">
													<img height="1" width="100%" border=0 alt="" src="/borrower/images/spacer.gif">
												</td>
											</tr>
											
											<tr>
									          <contentManager:NotFDRFlow>
									          <!-- Logic to show "Print Statements and Documents" link -->
													<td style="padding: 7px;" valign="top" width="50%">
														<a href="javascript:Submit('menu_SystemLetters')">
															<%=slmSession.getString(resourceID + ".PrintStatementsAndDocuments")%>
														</a>																
													</td>
													
													
													<td style="border-left: 1px solid #dfdfdf; padding: 7px;" valign="top" width="50%">
														<omniture:linkTracking trackingName="Media Server" href="#" onClick="javascript:Submit('global_LinkToMediaServer')">
														<%=slmSession.getString(resourceID + ".ViewCorrespondence")%>
														</omniture:linkTracking>
													</td>
												
												</contentManager:NotFDRFlow>
												
												 
												<contentManager:FDRFlow>
												<!-- Logic to show "Print Statements and Documents" link -->
													<td style="padding: 7px;" valign="top" width="50%">
														<a href="javascript:Submit('menu_fdrSystemLetters')">
															<%=slmSession.getString(resourceID + ".PrintStatementsAndDocuments")%>
														</a>
													</td>
													
													<td style="border-left: 1px solid #dfdfdf; padding: 7px;" valign="top" width="50%">
														<omniture:linkTracking trackingName="Media Server - FDR" href="#" onClick="javascript:Submit('global_LinkToMediaServer')">
														<%=slmSession.getString(resourceID + ".ViewCorrespondence")%>
														</omniture:linkTracking>
													</td>													
												</contentManager:FDRFlow>
											</tr>
											<tr>
											<td style="padding: 7px;" valign="top" width="50%">
											   <contentManager:PrimaryBorrower>
															<%=slmSession.getString(resourceID + ".PrintStatementsAndDocumentsNote_PrimaryBorrower")%>
												</contentManager:PrimaryBorrower>
												<contentManager:NotPrimaryBorrower>
															<%=slmSession.getString(resourceID + ".PrintStatementsAndDocumentsNote_Non_PrimaryBorrower")%>
												</contentManager:NotPrimaryBorrower>
												</td>
												<td style="border-left: 1px solid #dfdfdf; padding: 7px;" valign="top" width="50%">
													<%=slmSession.getString(resourceID + ".ViewCorrespondenceNote")%>
												</td>
												
																							
								             </tr>
												
											   <contentManager:NotFDRFlow>
											   <tr>
												<td style="border-top: 1px solid #dfdfdf; padding: 7px;" valign="top" width="50%">												 
												 <a href="javascript:Submit('global_paymentHistory')">
												    <%=slmSession.getString(resourceID + ".RecentPaymentsPosted") %>
												 </a>
												 </td>
												
										     	<contentManager:PrimaryBorrower>
										     	<td style="border-left: 1px solid #dfdfdf; border-top: 1px solid #dfdfdf; padding: 7px;"  valign="top" width="50%">
													<% if (pendingAndScheduledBillsList != null && pendingAndScheduledBillsList.size() >0) {%>
													<a href="javascript:Submit('menu_EBPP_ViewDelete')">
														<%=slmSession.getString(resourceID + ".PendingPayments")%>
													</a>
													<% } else {%>
													  <font color="#008cc7">
													      <%=slmSession.getString(resourceID + ".PendingPayments")%>
													  </font>
	            									<% }%>
												
												</td>
												</contentManager:PrimaryBorrower>
												<contentManager:NotPrimaryBorrower>
												<td style="border-left: 1px solid #dfdfdf; border-top: 1px solid #dfdfdf; padding: 7px;"  valign="top" width="50%">
													<a href="javascript:Submit('global_benefit')">
														<%=slmSession.getString(resourceID + ".BorrowerBenefits")%>
													</a>
												</td>
												</contentManager:NotPrimaryBorrower>
									    	
									    	
									    	</tr>
										
										<tr>
										<td valign="top" width="50%">
					<table border="0" cellpadding="2" cellspacing="0" width="100%">
           <%
            if ("true".equals(slmSession.getString("response.NoLastFivePayments"))){
          %>
          <tr>
            <td colspan="4" align="left"><%=slmSession.getString(resourceID + ".NoRecentPayments") %></td>
            <td><img src="/borrower/images/spacer.gif" alt=""></td>
          </tr>
          <%
            }else {
        
              for (int counter=1; counter<4; counter++)
              {           
               %>
              <tr>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
                <td><strong><%= slmSession.getHtml(slmSession.getString("response.paymentDate"+counter)) %></strong></td>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
                <td align="right"><%= slmSession.getHtml(slmSession.getString("response.paymentTotalAmount"+counter)) %></td>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
              </tr>
              <%
             }
          }
            %>
            </table>
			 </td>
			 <contentManager:PrimaryBorrower>
										<td width="50%" style="border-left: 1px solid #dfdfdf; padding: 7px;" valign="top">
											
					<% if (recentDateAndPaymentPendingMapByConfirmationNumber != null && recentDateAndPaymentPendingMapByConfirmationNumber.size() > 0) { %>
						<table border="0" cellpadding="1" cellspacing="0" width="100%">
						
						<%for (String confirmationNumber : recentDateAndPaymentPendingMapByConfirmationNumber.keySet()) { %>
						     <% for(Date recentPendingPaymentDate: (recentDateAndPaymentPendingMapByConfirmationNumber.get(confirmationNumber)).keySet())  { %>
						  <tr>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
                <td><strong>
                   <%= slmSession.getHtml( dateFormatter.format(recentPendingPaymentDate)) %>
                </strong></td>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
                <td align="right">$<%= slmSession.getHtml((recentDateAndPaymentPendingMapByConfirmationNumber.get(confirmationNumber)).get(recentPendingPaymentDate).getFormattedValue()) %></td>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
              </tr>
						  <%}  %>
						  
						<% } %>	  
						  
              </table>
              <% } else { %>
											<%=slmSession.getString(resourceID + ".NoPendingPayments") %>
											<% } %>
										</td>
						</contentManager:PrimaryBorrower>				
										<contentManager:NotPrimaryBorrower>
										 <td width="50%" style="border-left: 1px solid #dfdfdf; padding: 7px;" valign="top">
												<%=slmSession.getString(resourceID + ".BorrowerBenefitsNote")%>
										</td>
										</contentManager:NotPrimaryBorrower>
									   </tr>
									   
									   <contentManager:PrimaryBorrower>
									   <tr>
									   <td style="border-top: 1px solid #dfdfdf;  padding: 7px;"  valign="top" colspan="2">
													<a href="javascript:Submit('global_benefit')">
														<%=slmSession.getString(resourceID + ".BorrowerBenefits")%>
													</a>
												</td>
									    </tr>
										<tr>
										<td colspan="2" valign="top">
												<%=slmSession.getString(resourceID + ".BorrowerBenefitsNote")%>
										</td>
									   </tr>
									  </contentManager:PrimaryBorrower> 
									</contentManager:NotFDRFlow>
									
									 <contentManager:FDRFlow>
											   <tr>
												<td style="border-top: 1px solid #dfdfdf; padding: 7px;" valign="top" width="50%">												 
												  <% if (pendingAndScheduledBillsList != null && pendingAndScheduledBillsList.size() > 0) {%>
												  <a href="javascript:Submit('menu_FDR_ViewDelete')">
												    <%=slmSession.getString(resourceID + ".PendingPayments") %>
												 </a>
												 <% } else { %>
												     <font color="#008cc7">
													      <%=slmSession.getString(resourceID + ".PendingPayments")%>
													  </font>
												 <% } %>
												 </td>
										     	
										     	
										     	<td style="border-top: 1px solid #dfdfdf; border-left: 1px solid #dfdfdf; padding: 7px;"  valign="top">
													<a href="javascript:SubmitMYL('/benefit/fdrBenefit')">
														<%=slmSession.getString(resourceID + ".BorrowerBenefits")%>
													</a>
												</td>
									    	</tr>
										
										<tr>
										<td style="padding: 7px;" valign="top" width="50%">
										
										<% if (pendingAndScheduledBillsList != null && pendingAndScheduledBillsList.size() > 0) {%>			
														<table border="0" cellpadding="1" cellspacing="0" width="100%">
						  <%for(int i=0; i<3 && i< pendingAndScheduledBillsList.size() ; i++) {%>
						  <tr>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
                <td><strong>
                   <%= slmSession.getHtml( dateFormatter.format(pendingAndScheduledBillsList.get(i).getPaymentSubmitDate())) %>
                </strong></td>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
                <td align="right">$<%= slmSession.getHtml(pendingAndScheduledBillsList.get(i).getPaymentAmount().getFormattedValue()) %></td>
                <td><img src="/borrower/images/spacer.gif" alt=""></td>
              </tr>
						  <%} %>
						  
              </table>
											<% } else { %>		
											<%=slmSession.getString(resourceID + ".NoPendingPayments") %>
											<% } %>	
										</td>	
										<td style="border-left: 1px solid #dfdfdf; padding: 7px;"  valign="top">
														<%=slmSession.getString(resourceID + ".BorrowerBenefitsNote")%>
										</td>
									   </tr>
									</contentManager:FDRFlow>
	                                      </table>
										</td>
									</tr>
								</table>
								<br>
								<!--  End Important Account Information--> <!-- InstanceEndEditable -->							              
								   </td>
								        
								</tr>							
							
							</table>
					
						</td>
					
						<td valign="top" width="300"> <!-- Right column -->
					
							<table border="0" width="100%" cellpadding="0" cellspacing="0">
								 <tr>  <!-- Top right Body content goes here. !-->
								      <td valign="top" width="100%">									
			    	                      <!-- InstanceBeginEditable name="top_right_body" -->
							<%@ include file="/borrower/includes/advisor.jsp" %>
							<br>
							
							<contentManager:PrimaryBorrower>
					             <%@include file="/borrower/includes/upromiseRewards.jsp" %>
					        </contentManager:PrimaryBorrower>



			<%-- TeaLeaf comments for CMS --%>
			<!--
			<BRData>
  			<ID><%= slmSession.getString("MarketingMessages.ssn") %></ID>
  			<NSNCBA><%= slmSession.getString("MarketingMessages.NSNCBA") %></NSNCBA>
  			<NSCBA><%= slmSession.getString("MarketingMessages.NSNCBA") %></NSCBA>
  			<CFCI><%= slmSession.getString("MarketingMessages.CFCI") %></CFCI>
  			<CFA><%= slmSession.getString("MarketingMessages.CFA") %></CFA>
  			<CPCI><%= slmSession.getString("MarketingMessages.CPCI") %></CPCI>
  			<CPA><%= slmSession.getString("MarketingMessages.CPA") %></CPA>
  			<CMSMessagesDisplayed>
    		<Language><%= (slmSession.getString("LANG_PREF_LOCALE").equals("es") ? "Spanish" : "English") %></Language>
			<%
			String strMsgCount = slmSession.getString("MarketingMessages.count");
			if (strMsgCount != null && !"".equals(strMsgCount)) {
    		try {
        	int msgCount = Integer.parseInt(strMsgCount);
       			 for (int i=1; i<=msgCount; i++) {
					%>
    				<CMSMessageDisplayed<%= i %>>
      				<Target<%= i %>><%= slmSession.getString("MarketingMessages" + i + ".targetName") %></Target<%= i %>>
      				<MsgID<%= i %>><%= slmSession.getString("MarketingMessages" + i + ".MsgID") %></MsgID<%= i %>>
      				<PubDate<%= i %>><%= slmSession.getString("MarketingMessages" + i + ".pubDate") %></PubDate<%= i %>>
    				</CMSMessageDisplayed<%= i %>>
					<%
        			}
    				} catch (Exception e) {
    				}
					}
					%>
  			</CMSMessagesDisplayed>
			</BRData>
			-->	  	 
			
			
						 <!-- InstanceEndEditable -->			                       
									  </td>
								 </tr>
								 
								 <tr>  <!-- Bottom right Body content goes here. !-->
						         	<td valign="top" width="100%">	
					                	<!-- InstanceBeginEditable name="bottom_right_body" -->
			<!--  Start Ad Rotaror -->
			
				<contentManager:SallieMae>
                 	<%-- CrossSellAd:2010-10 --%>
				 	<% locationID = "MYL001"; %>
				 	<!--  <ABTest>B </ABTest> -->
				  	<%@include file="/borrower/includes/printCrossSellAds.jsp" %>
				</contentManager:SallieMae>
			
			
			             	
			<!--  End Ad Rotaror -->		                	
						<!-- InstanceEndEditable -->			                 
									</td>
							   </tr>
							</table>
					
						</td>
					</tr>
				  </table>
				  
			   </td>
			 </tr>
			 
			 <tr>&nbsp;</tr>
			 <tr>	<!-- Footer Row --></tr></table> 
			 <!-- #BeginLibraryItem "/Library/footer.lbi" --><%if(slmSession.isDepartmentOfEd()){%>
	<%@ include file="/borrower/includes/edGovFooter.jsp" %> 
<%}else{%>
	<%@ include file="/borrower/includes/mylFooter.jsp" %>
<%}%>
<!-- #EndLibraryItem --><table border="0" width="766"><tr><%@include file="/borrower/includes/site_catalyst.jsp" %>
	         </tr>
							
 </table> <!--Main Table Ends-->

</body>
<!-- InstanceEnd --></html>

