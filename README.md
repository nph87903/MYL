MYL
===\
/borrower/payment/ebpp_paynow.jsp for Borrower paynow flow

<%@page import="java.io.PrintWriter"%>
<%@page import="com.slma.utils.MoneyUSD"%>
<%@page import="java.math.BigDecimal"%>
<%@page import="java.math.RoundingMode"%>
<%@page import="java.math.MathContext"%>
<%@page import="com.slma.framework.Controller"%>
<%@page import="com.slma.framework.ControllerImpl"%>

<%@page import="java.util.List"%>
<%@page import="java.util.TreeMap"%>
<%@page import="com.slma.selfservice.core.Platform" %>
<%@page import="com.slma.selfservice.borrower.payment.ElectronicBill"%>
<%@page import="com.slma.selfservice.borrower.payment.PaymentDate"%>
<%@include file="/WEB-INF/jsp/includes/tagLib.jsp" %>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd"><html><!-- InstanceBegin template="/templates/slm_base_template_lockdown.dwt" codeOutsideHTMLIsLocked="false" -->
<%String dwtName="slm_base_template_lockdown.dwt"; %>
<%String headerName="header_lockdown"; %>
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
<%@include file="/borrower/includes/omnitureTrackingWorkaroundScripts.jsp" %>
<%
	TreeMap<ElectronicBill, List<ElectronicBill>>  billsMap = (TreeMap) slmSession.get("classMonthlyBills");

	needsToggle=true;
	
	String prefix = "" ;
	String cosignPmtStr = "";
    String cosignPrincipleBalance = "";
    
    Money cosignTotalPrincipleBalance = new MoneyUSD(0);
	Money totalPmtAmount = new MoneyUSD(0);
	Money totalCosingPmtAmount = new MoneyUSD(0);
	
	String resourceID = new com.slma.selfservice.core.LanguageManager().initJSP(slmSession, "payment", "ebpp_paynow");
	String sharedResourceID = new com.slma.selfservice.core.LanguageManager().initJSP(slmSession, "payment", "paynow_shared");
%>
<fmt:setBundle basename="com.slma.selfservice.controller.resourceBundles.global.globalResources" var="lang" />
<fmt:setBundle basename="com.slma.selfservice.controller.resourceBundles.payment.paymentResources"/>
<c:set value="global.platform.description" var="resourceKey"></c:set>
<c:set value="paynow_shared" var="sharedResourceID"></c:set>
<title><%= slmSession.getString(resourceID + ".PageTitle") %></title>
<script type="text/javascript" src="/borrower/scripts/browserDetect.js"></script>
<script language="JavaScript">

function processCreditCardDialogNextClick()
{
    var isPaymentOptionRadioChecked = false;
    var creditCardPaymentOptionRadios = document.getElementsByName('creditCardPaymentOption');
    
    for (var i = 0; i < creditCardPaymentOptionRadios.length; i++) {
        if (creditCardPaymentOptionRadios[i].type === 'radio' && creditCardPaymentOptionRadios[i].checked) {
            isPaymentOptionRadioChecked = true;
            break;
        }
    }
    
    if (isPaymentOptionRadioChecked){
    	processCreditCardDialogNextClickByPlatform('<%=Platform.Commercial%>');
    }
    else{
    	triggerDynamicErrorTracking('You must select an account type.', 'omnitureTrackingWorkaroundTag');
    	document.getElementById('paymentOptionErrorElement').style.display='';
    }
}

var billGroupLoanArray = new Array();

function doShowHideOnLoad(){
	//if any of the sub electronicBill fields have value, then do the show function
	for(var x=0; x< billGroupLoanArray.length; x++){
		//get all the elements for the group
		var groupId = billGroupLoanArray[x];
		var loanInputElements = getElementsByClassName(document.getElementById(toggleContainer), "input", "sub" +groupId);
		for(var y=0; y< loanInputElements.length; y++){
			var showDetail = false;
			var ele = loanInputElements[y];
			if(ele.type.toLowerCase() == "checkbox" && ele.checked == true){
				showDetail = true;
			}
			if(ele.type.toLowerCase() == "text" && ele.value != ""){
				showDetail = true;
			}
			if(showDetail){
				
			}
			
			//hideSection(bgInputElements[i]);
		}
	}
}

	

function selectPayOnRadioButton(radioButton, radioButtonValue){
	for(var i=0;i<radioButton.length;i++){
		if(radioButton[i].value==radioButtonValue){
			radioButton[i].checked = true;
		}
	}
}

//End of Spetember 2011 Release
</script>
<%@ include file="/borrower/includes/paymentScripts.jsp" %>
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
		<!-- #BeginEditable "dev" -->
			<input type="hidden" name="ReturningFromPaynow" value="true" />
			<input type="hidden" name="Key1" value="" /> 
			<input type="hidden" name="Key2" value="" /> 
			<%
				// Make sure the Make a Payment tab is highlighted
				pageType = 3;
			%> 
		<div class="lightBoxOverlay" id="standardBlueLightbox"></div>

			<!-- #EndEditable -->		
    </form>
			
    <table border="0" width="766"  cellpadding="0" cellspacing="0"> <!--Main Table-->
	       <tr>	<!-- Row 1:Header Row Begins -->
		       <td><!-- #BeginLibraryItem "/Library/header_main.lbi" --><%if(slmSession.isDepartmentOfEd()){%>
	<%@ include file="/borrower/includes/edGovHeader.jsp" %> 
<%}else{%>
	<%@ include file="/borrower/includes/mylHeader.jsp" %>
<%}%>
<!-- #EndLibraryItem --></td>
		   </tr> <!--Row 1: Header Row ends-->
		   
		    <tr>	<!-- Row 2:Page Title Row Begins -->
		     <td>
		         <table width="100%" border="0" cellpadding="0" cellspacing="0"><!--Table for Page Title Begins-->
				 	<tbody>
			         <tr> 
				        <td width="100%"> <!--Table Data value for Pagetitle begins-->
					         <table width="100%" border="0" cellpadding="0" cellspacing="0"> <!--Table for PageTitle Begins-->
						     	<tbody>		 								
								<tr>
									<td><img src="/borrower/images/spacer.gif" height="1" width="364" alt=""></td>
					                <td><img src="/borrower/images/spacer.gif" height="1" width="400" alt=""></td>
								</tr>		 
								 <tr> <!--PageTitle row begins-->
							        <td width="40%" align="left" class="textheader"><!-- Page Title -->
						    	        <!-- InstanceBeginEditable name="page_title" -->
                                            <%=slmSession.getString(sharedResourceID + ".MakeAPmt")%>
                                				<br/>
                                				<br/>
                                				<br/>
                                				<br/>
                                				<br/>
                                <!-- InstanceEndEditable -->							        
									</td>
							        <td width="60%" align="right"><!-- Progress Bar -->
						    	        <!-- InstanceBeginEditable name="progress_bar" -->
						    	        <jsp:include page="/WEB-INF/jsp/common/orangeProgressBar.jsp" flush="true">       
                                  				<jsp:param name="pageFlow" value="Pay Now" />
                                  				<jsp:param name="currentStep" value="1" />
                                  				<jsp:param name="progressBarStyle" value="progress-bar-old-modified" />
                                			</jsp:include>							                                        <!-- InstanceEndEditable -->							        
									</td>														
						         </tr><!--PageTitle row ends-->
			              		</tbody>
						  </table> <!--Table for PageTitle Ends-->			           
					   </td>  <!--Table Data value for Pagetitle Ends-->
					 </tr>
				 </tbody>
			   </table>
		         <!--Table for Page Title Ends-->
		     </td>
      </tr>  <!-- Row 2:Page Title Row Ends -->
	  <%@ include file="/borrower/includes/errormessages.jsp" %> 
			 <tr>
        		<td colspan="3"><div id="globalErrorSection"> </div></td>
      		</tr>
			 <tr> <!--Row3: Body Row-->
			  <td colspan="2">
			   	  <!-- InstanceBeginEditable name="EditRegionForSLMBaseWideTemplate" -->
					<!-- EditRegionForSLMBaseWideTemplate --> 
					<form action="<%= slmSession.getString("response.PostAction") %>"
						method="POST" name="editform" id="editform">
						<input type="hidden" name="Select" value='<%= slmSession.getString("response.Select") %>' />
						<input type="hidden" name="StateID"	value='<%= slmSession.getString("response.StateID") %>' />
						<input type="hidden" name="ReturningFromPaynow" value="true" />
			
						<input type='hidden' id='adWarningAutoDebit' name='adWarningAutoDebit' value='<%=slmSession.getHtml(slmSession.getString("EBPPPayment.AutoDebit"))%>' />
						<input type='hidden' id='adWarningShown' name='adWarningShown' value='<%=slmSession.getHtml(slmSession.getString("EBPPPayment.adWarningShown")) %>' />
						<input type='hidden' id='autoDebitEnrolled' name='autoDebitEnrolled' value='' />
						<input type='hidden' id='rushPaySelected' name='rushPaySelected' value='' />
						<input type='hidden' id='creditCardAvailable' name='creditCardAvailable' value='' />
						<input type='hidden' id='envSiteCatalyst' name='envSiteCatalyst' value='<%= slmSession.getString("request.envSiteCatalyst") %>' />
						
		   	 			<div style="display:none;">	
							<omniture:linkTracking href="#" id="omnitureTrackingWorkaroundTag" trackingName="OmnitureTrackingWorkaround" />			    	
		    			</div>	
						<table class="platform" border="0" cellpadding="0" cellspacing="0">
							<!-- Platform Module  -->
							<tbody>
								<tr>
									<td>
										<table class="layout_table" border="0" cellpadding="0" cellspacing="0" width="100%">
											<!-- Payment Information  -->
											<tbody>
												<tr>
													<td><img src="/borrower/images/spacer.gif" height="1" alt=""></td>
												</tr>
												
														<%String platform = ""; %>
														<contentManager:SallieMae>
															<%platform = "Commercial"; %>
														</contentManager:SallieMae>
														<contentManager:DepartmentOfEd>
															<%platform = "ED"; %>
														</contentManager:DepartmentOfEd>
													<tr>
													<td><h1>
															<%= slmSession.getString(globalResourceID + ".platform.description." + platform) %>
															<%= slmSession.getString(globalResourceID + ".platform.description.loansText") %>
																		
														</h1>
													</td>
												</tr>
													<%
												boolean singleBill = (1 >= Integer.parseInt(slmSession.getString("response.payableBillsNum")));
												boolean payimm = (Boolean.parseBoolean(slmSession.get("response.showPayIMM").toString()) && 
																	singleBill && !slmSession.isDepartmentOfEd() );
						
	    										if (slmSession.get("hasBillsToPay") != null) {
	    	    	
											%>		
												<tr>
                          							<td class="bodynorm"><br>
                           									 <%=slmSession.getString(sharedResourceID + ".InRepayInstructionText")%>
                        						</tr>
	                        				</tbody>
	                        			</table>
	                        			<br>				
										
											<contentManager:NotPrimaryBorrower>
												<%@ include file="/borrower/payment/ebpp_paynow_cosigner.jsp"%>
											</contentManager:NotPrimaryBorrower>
					<%} %>
											
												<div id="billGroupSummary">
													<contentManager:PrimaryBorrower>	
													<%if(slmSession.get("response.Bill0.BillingItemNumber") != null){ %>
													<table class="billinggroups" border="0" cellpadding="0" cellspacing="0">
														<tbody>
															<tr valign="bottom"> <!-- New Header -->
																
	                            								<th><img src="/borrower/images/spacer.gif" height="44" width="1" alt=""></th>
	                            								<th align="left"><fmt:message key="${sharedResourceID }.LoanName"/></th>
	                            								<th align="center"><fmt:message key="${sharedResourceID }.LoanBalance"/></th>
	                         								   	<th align="center"><fmt:message key="${sharedResourceID }.CurrentInterestRate"/></th>
	                            								<th align="center"><font color="#000000"><strong><fmt:message key="${sharedResourceID }.MinimumPaymentDue"/></strong></font></th>
	                            								<th align="center"><fmt:message key="${sharedResourceID }.PaymentDueDate"/></th>
	                            								
	                          								</tr>
	                          								<tr>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="20" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="320" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="101" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="94" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="100" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="107" alt=""></td>
	                          								</tr>
															
																			<%		
	  									      									int billNumber = 0;
																				int schedCount = 0;
																				
																				String pmtStr = "";
																				
																		        // Loop through each bill in the session	
																		        //while (slmSession.get("response.Bill" + billNumber + ".BillingGroupItemNumber") != null) {																	        
																		         while (slmSession.get("response.Bill" + billNumber + ".BillingItemNumber") != null) {%>
																		         <tr>
																<td colspan="6">
																	<table width="100%" border="0" cellspacing="0" cellpadding="0" class="datatable">						
																		<tbody>
																		
																		        	 <%
																		        	 //System.out.println("From JSP, billNumber=" + billNumber);
																		             prefix = "response.Bill" + billNumber + ".";										            
																		         
																		           pmtStr= slmSession.getString(prefix + "PaymentAmount");
																		           if(pmtStr != null && !pmtStr.equals("")){
																						totalPmtAmount = totalPmtAmount.add(Double.valueOf(pmtStr));
																		           }
																		           else{
																		        	   pmtStr = "0.00";
																		           }
																			%>
																			<c:set value='<%=slmSession.getString(prefix + \"Pay\")%>' var="ddValue"/>
																			
														<c:set var="paytoday" value='<%=PaymentDate.TWO_TO_FOUR_DAYS.getValue()%>'/>
														<c:set var="rushPay" value='<%=PaymentDate.RUSH_PAY.getValue()%>'/>
														<c:set var="future" value='<%=PaymentDate.FUTURE_PAY.getValue()%>'/>
														<c:set var="futureSelected" value=""/>
														<c:set var="rushPaySelected" value=""/>
														<c:set var="paytodaySelected" value=""/>
														<c:choose>
														<c:when test="${future eq ddValue }">
															<c:set var="futureSelected" value="selected" ></c:set>
														</c:when>
														<c:when test="${rushPay eq ddValue }">
															<c:set var="rushPaySelected" value="selected" ></c:set>
														</c:when>
														<c:otherwise>
															<c:set var="paytodaySelected" value="selected" ></c:set>
														</c:otherwise>														
														</c:choose>																																																																					
																<%@ include file="/borrower/payment/ebpp_paynow_loan_by_billgroup.jsp" %>	
																																	
	                      														
	                      													</tbody>
	                                  								</table>
	                                  							</td>
	                                  						</tr>	
																			<% billNumber++;
	        																} // End of bill loop %>																	
	                                  										
	                                  									
	                                  						<tr>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="100%" alt=""></td>
	                          								</tr>
	                        							</tbody>
	                      							</table>
	                      							<input type="hidden" id="totalBills" value='<%=billNumber%>'/>
	                      							<%} %>
	                      							</contentManager:PrimaryBorrower>
	                      			<% if (payimm){%> <input type="hidden" name="payimmediately" value="true" /> <% } %>
	                      						  <%// Only show the loan section if there are eligible loans
	    																			// Your Loan(s) Not Yet in Repayment Section
	    																			if (slmSession.get("response.Loan0.LoanNumber") != null) { %>
								        											
	                      							<table class="layout_table" border="0" cellpadding="0" cellspacing="0" width="100%">
	                       								<!-- Payment Information  -->
	                        							<tbody>
	                          								<tr>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" alt=""></td>
	                         								</tr>
	                          								<tr>
	                            								<td class="bodynorm"><br>
	                              									<strong><fmt:message key="${sharedResourceID }.NotInRepaySectionHeader" /></strong></td>                 				
	                          								</tr>	      
	                          								<tr>
	                          									<td class="bodynorm"><br>
                              									<br><fmt:message key="${sharedResourceID }.NotInRepayInstructionText" /><jqueryPlugin:tooltip toolTipType="normalTip" toolTipTitle='<%= slmSession.getString(resourceID + ".PayALittle")%>' /></td>
                              									
	                          								</tr>
	                          								
	                        							</tbody>
	                      							</table>
	                      							<table class="billinggroups" border="0" cellpadding="0" cellspacing="0" >
	                        							<tbody>
	                          								<tr valign="bottom">
																<th><img src="/borrower/images/spacer.gif" height="44" width="1" alt=""></th>
																<th align="left"><fmt:message key="${sharedResourceID }.LoanName"></fmt:message></th>
																<th align="center"><fmt:message key="${sharedResourceID }.LoanBalance" /></th>
																<th align="center" ><fmt:message key="${sharedResourceID }.AccruedInterest"/></th>
																<th align="center"><font color="#000000"><strong><fmt:message key="${sharedResourceID }.MinimumPaymentDue"/></strong></font></th>															
																<th align="center"><fmt:message key="${sharedResourceID }.PaymentDueDate"/></th>
															</tr>
															<tr>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="20" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="320" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="101" alt=""></td>
	                           						 			<td><img src="/borrower/images/spacer.gif" height="1" width="94" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="100" alt=""></td>
	                            								<td><img src="/borrower/images/spacer.gif" height="1" width="107" alt=""></td>
	                          								</tr>
	                          								
	                                  										
	                                  											
	                                  											<%  	int loanNum = 0;
	                                  													double loanPmt = 0;
	        																			// Loop through each loan in the session
	        																			while (slmSession.get("response.Loan" + loanNum + ".LoanNumber") != null){ %>
	        																				<tr>
	        					                          									<td colspan="6">
	        					                          										<table class="datatable" border="0" cellpadding="0" cellspacing="0" width="100%">
	        					                                									<tbody>
	        																			<%
	            																			prefix = "response.Loan" + loanNum + ".";
										      												// The background alternates between gray and white
	            																			String backgroundClass = loanNum % 2 == 0  ? "class=\"bggray\"" : "";
	            																			String linkImage = loanNum % 2 == 0 ? "/borrower/images/glb_yellowarrow_gray.gif" : "/borrower/images/glb_yellowarrow.gif";
	
	            																			// Break up the amount into dollars and cents
	            																			String loanPmtStr= slmSession.getString(prefix + "PaymentAmount");
	            																			
	
	            																			try 
	            																			{
	            																				if(loanPmtStr != null && !loanPmtStr.equals("")){
	            																					loanPmt = Double.parseDouble(loanPmtStr);
	        																		           }
	        																		           else{
	        																		        	   loanPmtStr = "0.00";
	        																		           }
	            																					                																			
	            																			}  
	            																			catch (Exception ex)
	            																			{
	                																			// Use default values
	            																			}%>
	            																			
	            																			<input type="hidden" name="loanID" value='<%= slmSession.getString(prefix + "LoanNumber") %>' />
	            																			<tr class="bgLightBlue">
		            																			<td align="left"><img src="/borrower/images/spacer.gif" height="44" width="1" alt=""></td>																								
		            																			<td align="left" ><%= slmSession.getString(prefix + "Program") %></td>
																								<td align="center" >$<%= slmSession.getString(prefix + "CurrPrinBal")%></td>
																								<td align="center">$<%= slmSession.getHtml(slmSession.getString(prefix + "AccruedInterest")) %></td>
																								<td align="center">$<label for="Loan Payment Amount"><input id="Loan Payment Amount" name="LoanPaymentAmount<%= loanNum %>" Value ='<%= slmSession.getHtml(loanPmtStr) %>' size="8" maxlength="8" class="right_align" type="text"></label> </td>
																								<td align="center"><%=slmSession.getString(sharedResourceID + ".noData")%></td>																																													
																							</tr>
																							<tr>
                                    															<td><img src="/borrower/images/spacer.gif" height="2" width="1" alt=""></td>
                                    															<td><img src="/borrower/images/spacer.gif" height="2" width="303" alt=""></td>
                                    															<td><img src="/borrower/images/spacer.gif" height="2" width="84" alt=""></td>
                                    															<td><img src="/borrower/images/spacer.gif" height="2" width="77" alt=""></td>
                                    															<td><img src="/borrower/images/spacer.gif" height="2" width="83" alt=""></td>
                                    															<td><img src="/borrower/images/spacer.gif" height="2" width="90" alt=""></td>
                                  															</tr>
																							<tr>
																								<td colspan=6><img src="/borrower/images/spacer.gif" height="1" width="1" alt=""></td>
																							</tr>
																							
																							<tr class="bggray">
					                                  											<td><img src="/borrower/images/spacer.gif" height="24" width="1" alt=""></td>
					                                  											<td align="left"><font color="red">
																									<div id="errorattachloanCount<%=loanNum %>">
																										<%= slmSession.getErrorMessage("error.attach.loanCount" + loanNum) %>
																									</div>
																								</font></td>
																								<td colspan="2" align="right" class="bggray"><%slmSession.getString(sharedResourceID + ".PayThisAmountOnThisDate"); %></td>
																								<td align="center">$0.00</td>																							
																								<td align="left"><%=slmSession.getString(sharedResourceID + ".TWO_TO_FOUR_DAYS.selection")%></td>
					                                      										
																							</tr>																							
																							
																						<!-- Repayment Section End. -->	
																						<input type="hidden" id="totalLoans" value='<%=loanNum%>'/>
					</tbody>
	                                  								</table>
	                                  							</td>
	                          								</tr>
																				
	                                  										<% loanNum++;  } // End of pending payment loop %>
	                                  										 
	                                  									
														</tbody>
													</table>
													
													<%			
	    	} // End of if statement to check if there are billing groups	   	 
	 		%>
													
												
												</div>
													
			</tbody>
					</table>
												<table class="payment-info" border="0" cellpadding="0" cellspacing="0" width="100%" >
             									 <!-- Payment Information Module  -->
              										<tbody>
                										<tr>
                  											<td>
                  												<table class="layout_table" border="0" cellpadding="0" cellspacing="0" width="100%">
                      											<!-- Payment Information  -->
                      												<tbody>
                        												<tr>
                          													<td><img src="/borrower/images/spacer.gif" height="1" alt=""></td>
                        												</tr>
                        												<tr>
                          													<td><h1><%= slmSession.getString(resourceID + ".PaymentInformationTitle") %></h1></td>
                        												</tr>
                      												</tbody>
                    											</table>
                    											<table class="totalpay" border="0" cellpadding="0" cellspacing="0" width="100%">
                      												<tbody>
                        												<tr valign="bottom"> 
                          												<!--  <th></th> -->
                          													<th><img src="/borrower/images/spacer.gif" height="44" width="1" alt=""></th>
                          													<th align="left"></th>
                          													<th align="middle"><fmt:message key="${sharedResourceID }.TotalOutStandingBalance"/></th>
                          													<th align="middle"></th>
                          													<th align="middle"><font color="#000000"><strong><fmt:message key="${sharedResourceID }.TotalAmountToPay"/><br>
                            																	</strong></font></th>
                          													<th align="middle"><fmt:message key="${sharedResourceID }.PaymentSubmitted"/></th>
                        												</tr>
                        												<tr>
                          													<td><img src="/borrower/images/spacer.gif" height="1" width="20" alt=""></td>
                          													<td><img src="/borrower/images/spacer.gif" height="1" width="320" alt=""></td>
                          													<td><img src="/borrower/images/spacer.gif" height="1" width="101" alt=""></td>
                          													<td><img src="/borrower/images/spacer.gif" height="1" width="94" alt=""></td>
                          													<td><img src="/borrower/images/spacer.gif" height="1" width="100" alt=""></td>
                          													<td><img src="/borrower/images/spacer.gif" height="1" width="107" alt=""></td>
                        												</tr>
												                        <tr>
												                        
												                        	
												                        		
												                        
												                        	<c:set var="totalPmtAmount" value='<%=totalPmtAmount.getUnformattedValue()%>'/>
												                        	<c:set var="totalOutStandingBalanceBill" value='<%=slmSession.getString(\"response.TotalOutStandingBalanceBill\") %>'/>
												                        
												                        
												                        	
												                        	
												                        	<c:set var="loanTotal" value='<%=slmSession.getString(\"response.LoanTotal\") %>'/>
												                        	<c:set var="todayDate" value='<%=slmSession.getString(\"EBPP_Payment.todayDate\") %>'/>
												                        	
												                        	<c:set var="totalOutStandingBalanceBillAndloan" value="${totalOutStandingBalanceBill + loanTotal}"/>
                          													<td colspan="6">
                          														<table class="datatable" border="0" cellpadding="0" cellspacing="0" width="100%">
                              														<tbody>
                                														<tr class="bggray">
                                  															<td align="left"><img src="/borrower/images/spacer.gif" height="29" width="1" alt=""></td>
                                  															<td align="left"></td>        
                                  														                             														                             															
                                  															<td align="middle">$<fmt:formatNumber maxFractionDigits="2" value="${totalOutStandingBalanceBillAndloan}" pattern="#,##0.00;-#,##0.00" /> 
                                  															</td>
                                  															
                                  															<td align="middle"></td>                          															
                                  															<td align="middle">$<fmt:formatNumber maxFractionDigits="2" value="${totalPmtAmount}" pattern="#,##0.00;-#,##0.00" /></td>
                                  															<td align="middle"><c:out value="${todayDate}"></c:out></td>
                                														</tr>
                                														<tr class="bggray">
                                  															<td><img src="/borrower/images/spacer.gif" height="2" width="1" alt=""></td>
                                  															<td><img src="/borrower/images/spacer.gif" height="2" width="303" alt=""></td>
                                  															<td><img src="/borrower/images/spacer.gif" height="2" width="84" alt=""></td>
                                  															<td><img src="/borrower/images/spacer.gif" height="2" width="77" alt=""></td>
                                  															<td><img src="/borrower/images/spacer.gif" height="2" width="83" alt=""></td>
                                  															<td><img src="/borrower/images/spacer.gif" height="2" width="90" alt=""></td>
                                														</tr>
                              														</tbody>
                            													</table>
                            												</td>
                        												</tr>
                      												</tbody>
                    											</table>
                    										<!-- Button -->
                    											
	                    										<div style="width: 100%; text-align: right">			
																  <a href="javascript:Submit('CancelPayment')"><img border="0" height="31" width="76" src='/borrower/images/<%= slmSession.getString(resourceID + ".CancelImage") %>'
																	width="62" height="26" border="0" alt="Cancel"></a>
																	
																  <contentManager:PrimaryBorrower>
																		<contentManager:SallieMae>
																			<a href="javascript:submitPaymentEntryPage('<%=Platform.Commercial%>', false, 'bill', 'loan');"><img border="0" height="31" width="67" alt="Next" src='/borrower/images/<%= slmSession.getString(resourceID + ".NextImage") %>'></a>
														           		</contentManager:SallieMae>
														           		<contentManager:DepartmentOfEd>
														           			<a href="javascript:submitPaymentEntryPage('<%=Platform.ED%>', false, 'bill', 'loan');"><img border="0" height="31" width="67" alt="Next" src='/borrower/images/<%= slmSession.getString(resourceID + ".NextImage") %>' ></a>
														           		</contentManager:DepartmentOfEd>
																  </contentManager:PrimaryBorrower>
																  
																  <contentManager:NotPrimaryBorrower>
																		<contentManager:SallieMae>
																			<a href="javascript:submitPaymentEntryPage('<%=Platform.Commercial%>', false, 'cosignerLoan', 'loan');"><img border="0" height="31" width="67" alt="Next" src='/borrower/images/<%= slmSession.getString(resourceID + ".NextImage") %>' ></a>
														           		</contentManager:SallieMae>
														           		<contentManager:DepartmentOfEd>
																			<a href="javascript:submitPaymentEntryPage('<%=Platform.ED%>', false, 'cosignerLoan', 'loan');"><img border="0" height="31" width="67" alt="Next" src='/borrower/images/<%= slmSession.getString(resourceID + ".NextImage") %>' ></a>
														           		</contentManager:DepartmentOfEd>		  
																  </contentManager:NotPrimaryBorrower>
																</div>
																</td>
                											</tr>
              											</tbody>
            										</table>	
				
				<%
	        if ("true".equals(slmSession.getString("response.EndOfMonth")) && 
					1 >= Integer.parseInt(slmSession.getString("response.payableBillsNum"))){
	        	
	        %>
	        <contentManager:SallieMae>
	        <%
	        		if(Boolean.parseBoolean(slmSession.getString(prefix + "IsSchedulable"))){	        
			%>
				
					<tr>
						<td colspan="9"><fmt:message key="${sharedResourceID }.RushNotAvailable1"/><br>
						<br>
						</td>
					</tr>
					<%}else{%>
						<tr>
							<td colspan="9"><fmt:message key="${sharedResourceID }.RushNotAvailable2"/><br>
							<br>
							</td>
						</tr>
					<%
					}
					%>
				</contentManager:SallieMae>
				
				<%} %>
			
	 	
			
			
			
	
	
							
				</form>
	
		<!-- InstanceEndEditable -->
			   </td>
			 </tr>
			 
			 
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

