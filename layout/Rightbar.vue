<template>
  <div>
    <div class="right-bar">
      <simplebar class="h-100 right-scroll">
        <button
          type="button"
          class="btn-close close"
          aria-label="Close"
          @click="onTerminate"
        ></button>
        <add-associate-client
          v-if="displayAssociate"
          :key="rightBarKey"
          @updated="updated"
          @saved="confirm"
        ></add-associate-client>
        <add-new-client v-if="displayClient" @saved="onClose"></add-new-client>
        <add-edit-note v-if="displayCaseNote" @saved="onClose"></add-edit-note>
        <add-opportunity
          v-if="displayEnquiry"
          :key="rightBarKey"
          @request-close="onClose"
          @saved="onClose"
        ></add-opportunity>
        <client-access
          v-if="displayClientPermission"
          :key="rightBarKey"
          :display-error-only="true"
          @request-close="onClose"
        >
        </client-access>

        <case-add-documents
          v-if="addDocumentsShow"
          @cancel="onClose"
          @updated="updated"
        ></case-add-documents>
        <generate-document
          v-if="generateDocumentShow"
          @close="onClose"
          @updated="updated"
        ></generate-document>
        <upload-case-documents
          v-if="caseDocumentsShow"
          :category-source="category"
          @close="onClose"
        ></upload-case-documents>
        <case-task-add-edit
          v-if="displayCaseTask"
          :readonly="displayCaseTaskReadonly"
          @request-close="onClose"
        ></case-task-add-edit>
        <add-company v-if="displayAddCompany" @request-close="onClose"></add-company>
        <add-client-to-company v-if="displayAddClientToCompany" @request-close="onClose">
        </add-client-to-company>
        <chase-task-edit
          v-if="displayChaseTask"
          :readonly="displayChaseTaskReadonly"
          :openWorkflowSectionInNewTab="chaseTaskOpenWorkflowSectionInNewTab"
          :showWorkflowBackLink="showTodoBackLink"
          @confirm="confirm"
          @request-close="onClose"
        ></chase-task-edit>
        <!-- Create Mortgage Application -->
        <create-application
          v-if="displayCreateApplication"
          @request-close="onClose"
        ></create-application>
        <create-application-gi
          v-if="displayCreateApplicationGi"
          @request-close="onClose"
        ></create-application-gi>
        <create-protection-application
          v-if="displayCreateProtectionApplication"
          @request-close="onClose"
        >
        </create-protection-application>
        <case-things-todo
          v-if="displayTodoTask"
          :readonly="displayTodoTaskReadonly || !editPermission"
          :showBackLink="showTodoBackLink"
          :backLinkText="todoBackLinkText"
          @request-close="onClose"
        ></case-things-todo>
        <client-review-todo
          v-if="displayClientReviews"
          @request-close="onClose"
        ></client-review-todo>
        <unauthorised-client
          v-if="clientNoAccessPrompt && showClientNoAccessPrompt"
          :title="clientNoAccessPrompt.title_key"
          :firstname="clientNoAccessPrompt.first_name"
          :surname="clientNoAccessPrompt.surname"
          :dob="clientNoAccessPrompt.birth_date"
          :advisor="clientNoAccessPrompt.client_primary_advisor_id"
        />
        <create-referral
          v-if="displayCreateReferral"
          @request-close="onClose"
        ></create-referral>
        <create-opportunity v-if="displayCreateOpportunity" @request-close="onClose">
        </create-opportunity>
        <workload-validation
          v-if="displayWorkflowValidation"
          :openSectionInNewTab="workflowOpenSectionInNewTab"
          :showWorkflowBackLink="showWorkflowBackLink"
          @confirm="confirm"
          @request-close="onClose"
        >
        </workload-validation>
        <assign-compliance-officer
          v-if="displayAssignComplianceOfficer"
          @request-close="onClose"
        >
        </assign-compliance-officer>
        <!-- Create Commssion Schedule -->
        <create-commission-schedule
          v-if="displayCreateCommissionSchedule"
          @confirm="confirm"
          @request-close="onClose"
        ></create-commission-schedule>
        <!-- Create Commission Transaction -->
        <create-commission-transaction
          v-if="displayCreateCommissionTransaction"
          @confirm="confirm"
          @request-close="onClose"
        ></create-commission-transaction>
        <case-mark-application-complete
          v-if="displayCaseMarkApplicationComplete"
          @confirm="confirm"
          @request-close="onClose"
        >
        </case-mark-application-complete>
        <!-- Convert opportunity to Full Case -->
        <convert-opportunity-to-full-case
          v-if="displayConvertOpportunityToFullCase"
          @confirm="confirm"
          @request-close="onClose"
        ></convert-opportunity-to-full-case>
        <close-case v-if="displayCloseCase" @confirm="confirm" @request-close="onClose">
        </close-case>
        <client-note
          v-if="displayClientNote"
          @confirm="confirm"
          @request-close="onClose"
        ></client-note>
        <integration-panel v-if="showIntegrationPanel" />
        <off-canvas-submissions-brain-licence v-if="showNoSubmissionBrainLicence" />
        <!-- Submissions Brain DIP -->
        <off-canvas-dip-submission
          v-if="showSubmissionsBrainDip && selectedSubmissionsBrainDip"
        />
        <referral-quote-details
          v-if="showRequirementReferralQuote && selectedRequirementReferralQuote"
        />
        <off-canvas-conveyancing-quote v-if="showOffCanvasConveyancingQuote" />
      </simplebar>
    </div>

    <!-- Right bar overlay-->
    <div class="rightbar-overlay"></div>
  </div>
</template>

<script>
import AddNewClient from "../clients/files/AddNewClient.vue"
import AddAssociateClient from "../clients/files/AddAssociateClient.vue"
import AddEditNote from "../clients/files/AddEditNote.vue"
import CaseAddDocuments from "../clients/cases/CaseAddDocuments.vue"
import CaseTaskAddEdit from "../clients/cases/CaseTaskAddEdit.vue"
import AddOpportunity from "../clients/files/AddNewOpportunity.vue"
import ClientAccess from "../clients/files/ClientAccess.vue"
import ChaseTaskEdit from "../clients/files/ChaseTaskEdit.vue"
import GenerateDocument from "@/components/ux/shared/documents/GenerateDocument.vue"
import AddCompany from "../clients/files/AddCompany.vue"
import AddClientToCompany from "../clients/files/AddClientToCompany.vue"
import CreateApplication from "../clients/cases/requirements/mortgagerequirements/CreateApplication.vue"
import CreateApplicationGi from "../clients/cases/requirements/generalinsurance/recommendation/CreateApplication.vue"
import CreateProtectionApplication from "../clients/cases/requirements/protection/recommendation/CreateApplication.vue"
import CaseThingsTodo from "@/components/ux/shared/chaseTasks/CaseThingsTodo.vue"
import ClientReviewTodo from "@/components/ux/shared/chaseTasks/ClientReviewTodo.vue"
import simplebar from "simplebar-vue"
import { mapGetters, mapMutations, mapActions, mapState } from "vuex"
import UnauthorisedClient from "../clients/files/UnauthorisedClient.vue"
import CreateReferral from "@/components/ux/referrals/CreateReferral.vue"
import CreateOpportunity from "@/components/ux/clients/files/CreateOpportunity.vue"
import UploadCaseDocuments from "@/components/ux/shared/documents/UploadCaseDocuments.vue"
import WorkloadValidation from "../clients/cases/WorkflowValidation.vue"
import AssignComplianceOfficer from "@/pages/compliance/AssignComplianceOfficer.vue"
import CreateCommissionSchedule from "@/components/ux/commissions/CreateCommissionSchedule.vue"
import CreateCommissionTransaction from "@/components/ux/commissions/CreateCommissionTransaction.vue"
import CaseMarkApplicationComplete from "@/components/ux/clients/cases/CaseMarkApplicationComplete.vue"
import ConvertOpportunityToFullCase from "@/components/ux/opportunities/ConverToFullCase.vue"
import CloseCase from "@/components/ux/clients/cases/CloseCase.vue"
import IntegrationPanel from "../clients/cases/integration/IntegrationPanel.vue"
import { canPerformActionOnSelectedClient } from "@/global/modules/permissions/client-permissions"
import OffCanvasSubmissionsBrainLicence from "../clients/cases/requirements/mortgagerequirements/dips/OffCanvasSubmissionsBrainLicence.vue"
import OffCanvasDipSubmission from "../clients/cases/requirements/mortgagerequirements/dips/OffCanvasDipSubmission.vue"
import { useSlideout } from "@/global/modules/composables/useSlideout.ts"
import ClientNote from "@/components/ux/clients/notes/ClientNote.vue"
import ReferralQuoteDetails from "../clients/cases/requirements/mortgagerequirements/ReferralQuoteDetails.vue"
import OffCanvasConveyancingQuote from "../clients/cases/requirements/mortgagerequirements/OffCanvasConveyancingQuote.vue"


export default {
  name: "RightBar",
  components: {
    simplebar,
    AddNewClient,
    AddAssociateClient,
    AddEditNote,
    CaseAddDocuments,
    CaseTaskAddEdit,
    AddOpportunity,
    ClientAccess,
    AddCompany,
    AddClientToCompany,
    GenerateDocument,
    ChaseTaskEdit,
    CreateApplication,
    CreateApplicationGi,
    CaseThingsTodo,
    ClientReviewTodo,
    UnauthorisedClient,
    CreateReferral,
    CreateOpportunity,
    UploadCaseDocuments,
    CreateProtectionApplication,
    WorkloadValidation,
    AssignComplianceOfficer,
    CreateCommissionSchedule,
    CreateCommissionTransaction,
    CaseMarkApplicationComplete,
    ConvertOpportunityToFullCase,
    CloseCase,
    IntegrationPanel,
    OffCanvasSubmissionsBrainLicence,
    OffCanvasDipSubmission,
    ClientNote,
    ReferralQuoteDetails,
    OffCanvasConveyancingQuote
  },
  setup() {
    const { closeSlideout, terminateSlideout } = useSlideout()

    return {
      closeSlideout,
      terminateSlideout
    }
  },
  data() {
    return {
      category: "protection_advice",
      componentKey: 0,
      childData: null,
      editPermission: false
    }
  },
  computed: {
    ...mapState("integrationConfig", ["showIntegrationPanel"]),
    ...mapGetters({
      displayClient: "clients/showAddClient",
      displayAssociate: "clients/showAssociateClient",
      displayAddClientToCompany: "clients/showAddClientToCompany",
      rightBarKey: "clientFile/refreshRightBarKey",
      displayCaseNote: "clients/showNote",
      displayClientNote: "clients/showClientNote",
      clientNoAccessPrompt: "clientFile/clientNoAccessPrompt",
      showClientNoAccessPrompt: "clientFile/showClientNoAccessPrompt",
      addDocumentsShow: "cases/showAddCaseDocuments",
      generateDocumentShow: "documents/showGenerateDocument",
      caseDocumentsShow: "documents/showUploadDocument",
      displayCaseTask: "caseTasks/showTask",
      displayCaseTaskReadonly: "caseTasks/showReadonly",
      displayChaseTask: "tasks/showChaseTask",
      displayChaseTaskReadonly: "tasks/showChaseTaskReadonly",
      chaseTaskOpenWorkflowSectionInNewTab: "tasks/openWorkflowSectionInNewTab",
      displayTodoTask: "tasks/showTodoTask",
      displayClientReviews: "tasks/showClientReviewTask",
      displayTodoTaskReadonly: "tasks/showTodoTaskReadonly",
      showTodoBackLink: "tasks/showBackLink",
      todoBackLinkText: "tasks/backLinkText",
      displayEnquiry: "clients/showEnquiry",
      displayClientPermission: "clients/showClientPermission",
      displayAddCompany: "clients/showAddCompany",
      displayCreateApplication: "cases/showCreateApplicationMortgage",
      displayCreateApplicationGi: "cases/showCreateApplicationGi",
      displayCreateProtectionApplication: "cases/showCreateProtectionApplication",
      displayCreateReferral: "referrals/showCreateReferralSideMenu",
      displayCreateOpportunity: "clients/showCreateOpportunity",
      displayWorkflowValidation: "caseWorkflow/showWorkflowValidation",
      workflowOpenSectionInNewTab: "caseWorkflow/openSectionInNewTab",
      showWorkflowBackLink: "caseWorkflow/showWorkflowBackLink",
      displayAssignComplianceOfficer: "cases/showAssignComplianceOfficer",
      displayCreateCommissionSchedule: "cases/showCreateCommissionSchdule",
      displayCreateCommissionTransaction: "cases/showCreateCommissionTransaction",
      displayCaseMarkApplicationComplete: "cases/showCaseMarkApplicationComplete",
      displayConvertOpportunityToFullCase:
        "opportunities/showConvertOpportunityToFullCase",
      displayCloseCase: "cases/showCloseCase",
      showNoSubmissionBrainLicence: "requirements/showNoSubmissionsBrainLicence",
      selectedSubmissionsBrainDip: "requirements/selectedSubmissionsBrainDip",
      showSubmissionsBrainDip: "requirements/showSubmissionsBrainDip",
      showRequirementReferralQuote: "requirements/showRequirementReferralQuote",
      selectedRequirementReferralQuote: "requirements/selectedRequirementReferralQuote",
      showOffCanvasConveyancingQuote: "referrals/showOffCanCanvasConveyancingQuote"
    })
  },
  async mounted() {
    const canEdit = await canPerformActionOnSelectedClient(
      { cond: "any" },
      "all_areas",
      "edit"
    )

    this.editPermission = canEdit.success
  },
  beforeUnmount() {
    this.cleanUp()
  },
  methods: {
    ...mapMutations({
      addClientActive: "clients/setAddClientActive",
      associateClientActive: "clients/setAssociateClientActive",
      noteActive: "clients/setNoteActive",
      setClientNoteActive: "clients/setClientNoteActive",
      addDocsActive: "cases/setShowAddCaseDocuments",
      addTemplateActive: "documents/setShowGenerateDocument",
      closeCaseDocuments: "documents/setShowCaseDocuments",
      caseTaskActive: "caseTasks/setTaskActive",
      resetSectionAdminAction: "cases/setSectionAdminAction",
      enquiryActive: "clients/setNewOpportunityActive",
      addCompanyActive: "clients/setAddCompanyActive",
      addClientToCompanyActive: "clients/setAddClientToCompanyActive",
      chaseTaskActive: "tasks/setChaseTaskActive",
      todoTaskActive: "tasks/setTodoTaskActive",
      todoClientTaskActive: "tasks/setClientReviewTaskActive",
      createApplicationActive: "cases/setShowCreateApplicationMortgage",
      createApplicationGiActive: "cases/setShowCreateApplicationGi",
      setShowCreateProtectionApplication: "cases/setShowCreateProtectionApplication",
      clearApplicationDetails: "applications/clearApplicationDetails",
      setCreateReferralSideMenuStatus: "referrals/setCreateReferralSideMenuStatus",
      setCreateOpportunityActive: "clients/setCreateOpportunityActive",
      setShowWorkflowValidation: "caseWorkflow/setShowWorkflowValidation",
      setShowAssignComplianceOfficer: "cases/setShowAssignComplianceOfficer",
      setShowCreateCommissionSchedule: "cases/setShowCreateCommissionSchedule",
      setShowCreateCommissionTransaction: "cases/setShowCreateCommissionTransaction",
      setShowCaseMarkApplicationComplete: "cases/setShowCaseMarkApplicationComplete",
      setShowConvertOpportunityToFullCase:
        "opportunities/setShowConvertOpportunityToFullCase",
      setShowCloseCase: "cases/setShowCloseCase",
      setShowExperianAmlFlyout: "integrationConfig/setShowExperianAmlFlyout",
      setShowExperianCreditCheckFlyout:
        "integrationConfig/setShowExperianCreditCheckFlyout",
      setShowNoSubmissionsBrainLicence: "requirements/setShowNoSubmissionsBrainLicence",
      setSelectedSubmissionsBrainDip: "requirements/setSelectedSubmissionsBrainDip",
      setShowSubmissionsBrainDip: "requirements/setShowSubmissionsBrainDip",
      setSelectedRequirementReferralQuote:
        "requirements/setSelectedRequirementReferralQuote",
      setShowRequirementReferralQuote: "requirements/setShowRequirementReferralQuote"
    }),
    ...mapActions({
      clearClientNoAccessPrompt: "clientFile/clearClientNoAccessPrompt"
    }),
    onTerminate() {
      const result = this.childData !== null
      this.terminateSlideout(result, this.childData)
      this.closeAndCleanUp(result)
    },
    onClose() {
      const result = this.childData !== null
      this.closeSlideout(result, this.childData)
      this.closeAndCleanUp(result)
    },
    closeAndCleanUp(result) {
      this.$slideout.close(result, this.childData)
      this.cleanUp()
    },
    confirm(data) {
      this.closeSlideout(true, data)
      this.$slideout.close(true, data)
      this.cleanUp()
    },
    updated(data) {
      this.childData = data
    },
    cleanUp() {
      this.addClientActive(false)
      this.associateClientActive(false)
      this.noteActive(false)
      this.setClientNoteActive(false)
      this.addDocsActive(false)
      this.addTemplateActive(false)
      this.closeCaseDocuments(false)
      this.caseTaskActive(false)
      this.chaseTaskActive(false)
      this.todoTaskActive({ show: false, readonly: true })
      this.todoClientTaskActive(false)
      this.resetSectionAdminAction("")
      this.enquiryActive(false)
      this.addCompanyActive(false)
      this.addClientToCompanyActive(false)
      this.createApplicationActive(false)
      this.createApplicationGiActive(false)
      this.setShowCreateProtectionApplication(false)
      this.clearClientNoAccessPrompt()
      this.setCreateReferralSideMenuStatus({ show: false })
      this.setCreateOpportunityActive({
        status: false,
        type: "",
        productRecord: {},
        openRecord: false
      })
      this.clearApplicationDetails()
      this.setShowWorkflowValidation({ show: false, openSectionInNewTab: false })
      this.setShowAssignComplianceOfficer(false)
      this.setShowCreateCommissionSchedule(false)
      this.setShowCreateCommissionTransaction(false)
      this.setShowCaseMarkApplicationComplete(false)
      this.setShowConvertOpportunityToFullCase(false)
      this.childData = null
      this.setShowCloseCase(false)
      this.setShowExperianAmlFlyout(false)
      this.setShowExperianCreditCheckFlyout(false)
      this.setShowNoSubmissionsBrainLicence(false)
      this.setShowSubmissionsBrainDip(false)
      this.setSelectedSubmissionsBrainDip(false)
      this.setShowRequirementReferralQuote(false)
      this.setSelectedRequirementReferralQuote(null)
    }
  }
}
</script>

<style scoped>
:deep(.simplebar-scrollbar::before) {
  background-color: blue;
}

.slide-leave-active,
.slide-enter-active {
  transition: 0.3s;
}
.slide-enter {
  transform: translate(100%, 0);
}
.slide-leave-to {
  transform: translate(100%, 0);
}
</style>
