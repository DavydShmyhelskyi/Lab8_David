<script setup lang="ts">
import { computed, ref } from 'vue'

// Document interface
interface Document {
  id: number;
  number: string;
  name: string;
  createdOn: string;
  modifiedOn: string;
  fileSize: number;
  status: number;
  type: number;
  priority: 'high' | 'medium' | 'low';
  dueDate: string | null;
  author: string;
  department: string;
  approvalStage: number;
  totalStages: number;
  approvers?: Array<{
    approverId: number;
    name: string;
    position: string;
    stage: number;
    approved: boolean | null;
    approvedOn?: string | null;
    comment?: string | null;
    withRemarks: boolean;
  }>;
}

// Document metadata
const documentMetadata = {
  types: [
    { id: 1, name: 'Договір' },
    { id: 2, name: 'Акт' },
    { id: 3, name: 'Рахунок' },
    { id: 4, name: 'Накладна' },
    { id: 5, name: 'Інше' }
  ],
  statuses: [
    { id: 1, name: 'Новий', color: 'primary' },
    { id: 2, name: 'На погодженні', color: 'warning' },
    { id: 3, name: 'Погоджено', color: 'success' },
    { id: 4, name: 'Відхилено', color: 'error' },
    { id: 5, name: 'Підписано', color: 'primary' }
  ],
  approvers: [
    { id: 1, name: 'Іванов І.І.', position: 'Директор', department: 'Керівництво' },
    { id: 2, name: 'Петров П.П.', position: 'Фінансовий директор', department: 'Фінанси' },
    { id: 3, name: 'Сидоренко С.С.', position: 'Юрист', department: 'Юридичний відділ' },
    { id: 4, name: 'Коваленко К.К.', position: 'Головний бухгалтер', department: 'Бухгалтерія' }
  ],
  priorities: [
    { id: 'high', name: 'Високий', color: 'error' },
    { id: 'medium', name: 'Середній', color: 'warning' },
    { id: 'low', name: 'Низький', color: 'primary' }
  ]
}

// State variables
const pendingDocuments = ref<Document[]>([
  {
    id: 1,
    number: 'DOC-2023-001',
    name: 'Договір про надання послуг',
    createdOn: '2023-05-15T10:30:00',
    modifiedOn: '2023-05-16T14:20:00',
    fileSize: 1024 * 25,
    status: 2,
    type: 1,
    priority: 'high',
    dueDate: '2023-05-20T00:00:00',
    author: 'Петренко О.В.',
    department: 'Відділ продажів',
    approvalStage: 2,
    totalStages: 3,
    approvers: [
      {
        approverId: 1,
        name: 'Іванов І.І.',
        position: 'Директор',
        stage: 1,
        approved: true,
        approvedOn: '2023-05-17T09:30:00',
        comment: 'Погоджено без зауважень',
        withRemarks: false
      },
      {
        approverId: 2,
        name: 'Петров П.П.',
        position: 'Фінансовий директор',
        stage: 2,
        approved: null,
        approvedOn: null,
        comment: null,
        withRemarks: false
      },
      {
        approverId: 3,
        name: 'Сидоренко С.С.',
        position: 'Юрист',
        stage: 3,
        approved: null,
        approvedOn: null,
        comment: null,
        withRemarks: false
      }
    ]
  },
  {
    id: 2,
    number: 'DOC-2023-002',
    name: 'Акт виконаних робіт',
    createdOn: '2023-05-20T09:15:00',
    modifiedOn: '2023-05-21T11:45:00',
    fileSize: 1024 * 15,
    status: 2,
    type: 2,
    priority: 'medium',
    dueDate: '2023-05-25T00:00:00',
    author: 'Коваленко М.С.',
    department: 'Виробничий відділ',
    approvalStage: 1,
    totalStages: 2,
    approvers: [
      {
        approverId: 2,
        name: 'Петров П.П.',
        position: 'Фінансовий директор',
        stage: 1,
        approved: null,
        approvedOn: null,
        comment: null,
        withRemarks: false
      },
      {
        approverId: 4,
        name: 'Коваленко К.К.',
        position: 'Головний бухгалтер',
        stage: 2,
        approved: null,
        approvedOn: null,
        comment: null,
        withRemarks: false
      }
    ]
  },
  {
    id: 3,
    number: 'DOC-2023-003',
    name: 'Рахунок на оплату',
    createdOn: '2023-05-25T13:20:00',
    modifiedOn: '2023-05-25T15:10:00',
    fileSize: 1024 * 10,
    status: 2,
    type: 3,
    priority: 'low',
    dueDate: '2023-05-30T00:00:00',
    author: 'Сидоренко Т.М.',
    department: 'Фінансовий відділ',
    approvalStage: 1,
    totalStages: 2,
    approvers: [
      {
        approverId: 2,
        name: 'Петров П.П.',
        position: 'Фінансовий директор',
        stage: 1,
        approved: null,
        approvedOn: null,
        comment: null,
        withRemarks: false
      },
      {
        approverId: 4,
        name: 'Коваленко К.К.',
        position: 'Головний бухгалтер',
        stage: 2,
        approved: null,
        approvedOn: null,
        comment: null,
        withRemarks: false
      }
    ]
  }
]);

const approvalHistory = ref<ApprovalHistoryEntry[]>([
  {
    id: 101,
    documentId: 5,
    documentNumber: 'DOC-2023-005',
    documentName: 'Технічне завдання',
    approvedBy: 'Іванов І.І.',
    position: 'Директор',
    approved: true,
    approvedOn: '2023-06-01T14:20:00',
    comment: 'Погоджено',
    withRemarks: false
  },
  {
    id: 102,
    documentId: 5,
    documentNumber: 'DOC-2023-005',
    documentName: 'Технічне завдання',
    approvedBy: 'Сидоренко С.С.',
    position: 'Юрист',
    approved: true,
    approvedOn: '2023-06-02T11:30:00',
    comment: 'Погоджено з правками',
    withRemarks: true
  },
  {
    id: 103,
    documentId: 4,
    documentNumber: 'DOC-2023-004',
    documentName: 'Накладна №123',
    approvedBy: 'Коваленко К.К.',
    position: 'Головний бухгалтер',
    approved: false,
    approvedOn: '2023-05-28T10:05:00',
    comment: 'Помилка в сумі',
    withRemarks: false
  }
]);

const sortedApprovalHistory = computed(() =>
    approvalHistory.value
        .slice()
        .sort((a, b) => new Date(b.approvedOn).getTime() - new Date(a.approvedOn).getTime())
);

// Interface for approval history entries
interface ApprovalHistoryEntry {
  id: number;
  documentId: number;
  documentNumber: string;
  documentName: string;
  approvedBy: string;
  position: string;
  approved: boolean;
  approvedOn: string;
  comment: string;
  withRemarks: boolean;
}

// UI state
const activeTab = ref('pending');
const currentDocument = ref<Document | null>(null);
const isDocumentModalOpen = ref(false);
const approvalComment = ref('');
const approvalDecision = ref<'approve' | 'approve_with_remarks' | 'reject' | null>(null);
const delegateToUser = ref<number | null>(null);
const showDelegateForm = ref(false);

// Filter state
const search = ref('');
const selectedPriority = ref<{ id: string; label: string }[]>([]);
const selectedType = ref<{ id: number; label: string }[]>([]);
const dateRange = ref<[Date | null, Date | null]>([null, null]);

// Current user ID (in a real app would come from auth system)
const CURRENT_USER_ID = 2;

// Computed properties
const filteredDocuments = computed(() => {
  let filtered = [...pendingDocuments.value];

  if (search.value) {
    const searchLower = search.value.toLowerCase();
    filtered = filtered.filter(doc =>
        doc.number.toLowerCase().includes(searchLower) ||
        doc.name.toLowerCase().includes(searchLower)
    );
  }

  if (selectedPriority.value.length > 0) {
    const selectedIds = selectedPriority.value.map(p => p.id);
    filtered = filtered.filter(doc => selectedIds.includes(doc.priority));
  }

  if (selectedType.value.length > 0) {
    const typeIds = selectedType.value.map(t => t.id);
    filtered = filtered.filter(doc => typeIds.includes(doc.type));
  }

  if (dateRange.value[0] && dateRange.value[1]) {
    const startDate = dateRange.value[0].getTime();
    const endDate = dateRange.value[1].getTime();

    filtered = filtered.filter(doc => {
      const dueDate = new Date(doc.dueDate || '').getTime();
      return dueDate >= startDate && dueDate <= endDate;
    });
  }

  return filtered;
});

// Filtered approvers for delegation (excluding current user)
const filteredApprovers = computed(() =>
    documentMetadata.approvers.filter(a => a.id !== CURRENT_USER_ID)
);

// Document handling functions
function openDocument(document: Document) {
  currentDocument.value = document;
  approvalComment.value = '';
  approvalDecision.value = null;
  delegateToUser.value = null;
  showDelegateForm.value = false;
  isDocumentModalOpen.value = true;
}

function approveDocument() {
  if (!currentDocument.value || !approvalDecision.value) return;

  const docIndex = pendingDocuments.value.findIndex(doc => doc.id === currentDocument.value?.id);

  if (docIndex !== -1) {
    const doc = pendingDocuments.value[docIndex];
    if (!doc || !doc.approvers) return;

    const approvers = doc.approvers;
    if (approvers.length === 0) return;

    const approverIndex = approvers.findIndex(
        approver => approver.approverId === CURRENT_USER_ID
    );

    if (approverIndex !== -1) { // Removed unnecessary check since we already have approvers
      // Update approval status
      const isApproved = approvalDecision.value === 'approve' || approvalDecision.value === 'approve_with_remarks';
      const currentApprover = approvers[approverIndex]; // Use the local approvers variable

      if (currentApprover) {
        currentApprover.approved = isApproved;
        currentApprover.approvedOn = new Date().toISOString();
        currentApprover.comment = approvalComment.value || null;
        currentApprover.withRemarks = approvalDecision.value === 'approve_with_remarks';
      } else {
        return; // Exit if no approver found
      }

      // Update document stage and status
      updateDocumentStage(docIndex);

      // Add to approval history
      addApprovalHistoryEntry(docIndex, isApproved);

      // Show toast and close modal
      showResultToast(approvalDecision.value);
      isDocumentModalOpen.value = false;
    }
  }
}

function updateDocumentStage(docIndex: number) {
  const doc = pendingDocuments.value[docIndex];
  if (!doc) return;

  const currentStage = doc.approvalStage;

  const allApprovedInStage = doc.approvers?.filter(a => a.stage === currentStage)
      .every(a => a.approved !== null);

  if (allApprovedInStage) {
    const anyRejectedInStage = doc.approvers?.filter(a => a.stage === currentStage)
        .some(a => a.approved === false);

    if (anyRejectedInStage) {
      doc.status = 4; // Rejected
    } else if (currentStage < doc.totalStages) {
      doc.approvalStage = currentStage + 1;
    } else {
      doc.status = 3; // Approved
    }
  }
}

function addApprovalHistoryEntry(docIndex: number, isApproved: boolean) {
  const doc = pendingDocuments.value[docIndex];
  const currentUser = documentMetadata.approvers.find(a => a.id === CURRENT_USER_ID);

  if (!currentUser || !doc) return;

  approvalHistory.value.unshift({
    id: Date.now(),
    documentId: doc.id,
    documentNumber: doc.number,
    documentName: doc.name,
    approvedBy: currentUser.name,
    position: currentUser.position,
    approved: isApproved,
    approvedOn: new Date().toISOString(),
    comment: approvalComment.value || '',
    withRemarks: approvalDecision.value === 'approve_with_remarks'
  });
}

function delegateApproval() {
  if (!currentDocument.value || !delegateToUser.value) return;

  const docIndex = pendingDocuments.value.findIndex(doc => doc.id === currentDocument.value?.id);

  if (docIndex !== -1 && pendingDocuments.value[docIndex]?.approvers) {
    const approverIndex = pendingDocuments.value[docIndex].approvers!.findIndex(
        approver => approver.approverId === CURRENT_USER_ID
    );

    if (approverIndex !== -1) {
      const delegateUser = documentMetadata.approvers.find(a => a.id === delegateToUser.value);
      const approvers = pendingDocuments.value[docIndex].approvers;

      if (delegateUser && approvers && approvers[approverIndex]) {
        // Replace approver
        pendingDocuments.value[docIndex].approvers![approverIndex] = {
          approverId: delegateUser.id,
          name: delegateUser.name,
          position: delegateUser.position,
          stage: approvers[approverIndex].stage,
          approved: null,
          approvedOn: null,
          comment: `Delegated from: ${documentMetadata.approvers.find(a => a.id === CURRENT_USER_ID)?.name}`,
          withRemarks: false
        };

        // Add to history
        const currentUser = documentMetadata.approvers.find(a => a.id === CURRENT_USER_ID);

        approvalHistory.value.unshift({
          id: Date.now(),
          documentId: currentDocument.value.id,
          documentNumber: currentDocument.value.number,
          documentName: currentDocument.value.name,
          approvedBy: currentUser?.name || '',
          position: currentUser?.position || '',
          approved: true,
          approvedOn: new Date().toISOString(),
          comment: `Delegated to: ${delegateUser.name}`,
          withRemarks: false
        });

        showToast(
            'Delegated',
            `Approval delegated to: ${delegateUser.name}`,
            'blue'
        );

        isDocumentModalOpen.value = false;
      }
    }
  }
}

// Utility functions
function showResultToast(decision: string) {
  let title, description, color;

  if (decision === 'approve') {
    title = 'Success';
    description = 'Document approved successfully';
    color = 'emerald';
  } else if (decision === 'approve_with_remarks') {
    title = 'Approved with remarks';
    description = 'Document approved with remarks';
    color = 'amber';
  } else {
    title = 'Rejected';
    description = 'Document rejected';
    color = 'rose';
  }

  showToast(title, description, color);
}

function showToast(title: string, description: string, color: string) {
  // In a real project, use useToast() from Nuxt UI
  console.log(`[${color}] ${title}: ${description}`);
}

function resetFilters() {
  search.value = '';
  selectedPriority.value = [];
  selectedType.value = [];
  dateRange.value = [null, null];
}

function formatDate(dateStr: string | null) {
  if (!dateStr) return '';
  return new Date(dateStr).toLocaleString('uk-UA');
}

function getDocumentTypeName(typeId: number): string {
  const type = documentMetadata.types.find(t => t.id === typeId);
  return type ? type.name : 'Unknown';
}

function getPriorityInfo(priority: string): { name: string, color: "error" | "primary" | "warning" | "success" | "secondary" | "info" | "neutral" } {
  const priorityInfo = documentMetadata.priorities.find(p => p.id === priority);

  // Map the color strings to valid UBadge colors
  if (priorityInfo) {
    const colorMap: Record<string, "error" | "primary" | "warning" | "success" | "secondary" | "info" | "neutral"> = {
      'error': 'error',
      'primary': 'primary',
      'warning': 'warning',
      'success': 'success',
      'rose': 'error',
      'emerald': 'success',
      'amber': 'warning',
      'blue': 'primary',
      'gray': 'neutral'
    };

    return {
      name: priorityInfo.name,
      color: colorMap[priorityInfo.color] || 'neutral'
    };
  }

  return { name: 'Unknown', color: 'neutral' };
}

function isOverdue(dueDate: string | null): boolean {
  if (!dueDate) return false;
  return new Date(dueDate) < new Date();
}

function getApprovalProgress(doc: Document): number {
  if (!doc.approvers || doc.approvers.length === 0) return 0;

  const totalApprovers = doc.approvers.length;
  const approvedCount = doc.approvers.filter(a => a.approved !== null).length;

  return Math.round((approvedCount / totalApprovers) * 100);
}

function getProgressColor(doc: Document): string {
  if (doc.status === 4) return 'rose';
  if (doc.status === 3) return 'emerald';

  const progress = getApprovalProgress(doc);
  if (progress === 0) return 'gray';
  if (progress < 50) return 'blue';
  if (progress < 100) return 'amber';
  return 'emerald';
}

function canApprove(doc: Document): boolean {
  return doc.approvers?.some(
      a => a.approverId === CURRENT_USER_ID &&
          a.approved === null &&
          a.stage === doc.approvalStage
  ) || false;
}

function viewDocument(doc: Document) {
  currentDocument.value = doc;
  isDocumentModalOpen.value = true; // Це відкриває модальне вікно
  approvalComment.value = '';
  approvalDecision.value = null;
  delegateToUser.value = null;
  showDelegateForm.value = false;
  nextTick(() => {
    const el = document.getElementById('document-view');
    if (el) el.scrollIntoView({ behavior: 'smooth', block: 'center' });
  });
}
</script>

<template>
  <div class="min-h-screen bg-gray-50 dark:bg-gray-900 pb-10">
    <!-- <div class="bg-gradient-to-r from-blue-600 to-blue-800 dark:from-blue-800 dark:to-indigo-900 text-white px-6 py-8 mb-8 shadow-lg">
      <div class="container mx-auto">
        <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
          <div>
            <h1 class="text-3xl font-bold mb-2">Погодження документів</h1>
            <p class="text-blue-100 max-w-lg">Система електронного документообігу для ефективного управління процесами погодження</p>
          </div>
          <UButton
            color="primary"
            variant="solid"
            icon="i-heroicons-document-text"
            to="/documents"
            class="font-medium shadow-sm hover:shadow-md transition-all duration-200 bg-white text-blue-600 dark:bg-white dark:text-blue-800"
          >
            Всі документи
          </UButton>
        </div>
      </div>
    </div> -->

    <div class="container mx-auto px-4">
      <!-- Tabs -->
      <UTabs v-model="activeTab" :items="[
        { slot: 'pending', label: 'Очікують погодження', icon: 'i-heroicons-clock' },
        { slot: 'history', label: 'Історія погоджень', icon: 'i-heroicons-clock-rewind' }
      ]" class="mb-6" :ui="{
        list: 'bg-white dark:bg-gray-800 rounded-xl shadow-sm px-1 py-1',
        trigger: 'text-sm md:text-base py-2.5 px-4 font-medium rounded-lg transition-colors duration-200 text-gray-700 dark:text-gray-300 hover:text-primary-600 dark:hover:text-primary-400 hover:bg-gray-100 dark:hover:bg-gray-700/50 data-[active]:text-primary-600 data-[active]:dark:text-white data-[active]:bg-primary-50 data-[active]:dark:bg-primary-800/50'
      }">
        <template #pending>
          <!-- Filters -->
          <div class="bg-white dark:bg-gray-800 rounded-xl shadow-sm p-5 mb-6">
            <div class="grid grid-cols-1 md:grid-cols-4 gap-5">
              <UInput
                  v-model="search"
                  icon="i-heroicons-magnifying-glass-20-solid"
                  placeholder="Пошук по номеру та назві..."
                  class="focus-within:ring-2 focus-within:ring-primary-500 dark:focus-within:ring-primary-400 transition-shadow duration-200"
                  trailing
              />

              <USelectMenu
                  v-model="selectedPriority"
                  :items="[
                  { id: 'high', label: 'Високий' },
                  { id: 'medium', label: 'Середній' },
                  { id: 'low', label: 'Низький' }
                ]"
                  multiple
                  item-label="label"
                  item-value="id"
                  placeholder="Пріоритет"
                  class="focus-within:ring-2 focus-within:ring-primary-500 dark:focus-within:ring-primary-400 transition-shadow duration-200"
              />

              <USelectMenu
                  v-model="selectedType"
                  :items="[
                  { id: 1, label: 'Договір' },
                  { id: 2, label: 'Акт' },
                  { id: 3, label: 'Рахунок' },
                  { id: 4, label: 'Накладна' },
                  { id: 5, label: 'Інше' }
                ]"
                  multiple
                  item-label="name"
                  item-value="id"
                  placeholder="Тип документа"
                  class="focus-within:ring-2 focus-within:ring-primary-500 dark:focus-within:ring-primary-400 transition-shadow duration-200"
              />

              <UButton
                  color="primary"
                  variant="solid"
                  icon="i-heroicons-document-text"
                  to="/documents"
                  class="font-medium shadow-sm hover:shadow-md transition-all duration-200 bg-white text-blue-600 dark:bg-white dark:text-blue-800"
              >
                Всі документи
              </UButton>
              <!-- <div class="flex gap-2 w-full">
                <input
                  type="date"
                  :value="dateRange[0] ? dateRange[0].toISOString().substr(0,10) : ''"
                  @change="(e: Event) => { const target = e.target as HTMLInputElement; dateRange[0] = target.value ? new Date(target.value) : null; }"
                  placeholder="Початкова дата"
                  class="w-full focus-within:ring-2 focus-within:ring-primary-500 dark:focus-within:ring-primary-400 transition-shadow duration-200"
                />
                <input
                  type="date"
                  :value="dateRange[1] ? dateRange[1].toISOString().substr(0,10) : ''"
                  @change="(e: Event) => { const target = e.target as HTMLInputElement; dateRange[1] = target.value ? new Date(target.value) : null; }"
                  placeholder="Кінцева дата"
                  class="w-full focus-within:ring-2 focus-within:ring-primary-500 dark:focus-within:ring-primary-400 transition-shadow duration-200"
                />
              </div> -->

            </div>

            <div class="flex justify-end mt-5">
              <UButton
                  icon="i-heroicons-funnel"
                  color="neutral"
                  variant="ghost"
                  size="sm"
                  :disabled="!search && !selectedPriority.length && !selectedType.length && !dateRange[0]"
                  @click="resetFilters"
                  class="font-medium hover:bg-gray-100 dark:hover:bg-gray-700 transition-colors duration-200"
              >
                Скинути фільтри
              </UButton>
            </div>
          </div>

          <!-- Document List -->
          <div class="grid grid-cols-1 gap-6">
            <div
                v-for="doc in filteredDocuments"
                :key="doc.id"
                class="bg-white dark:bg-gray-800 rounded-xl shadow-sm hover:shadow-md transition-all duration-200 overflow-hidden"
            >
              <!-- Progress indicator -->
              <div class="h-1.5 bg-gray-100 dark:bg-gray-700">
                <div
                    class="h-full rounded-r-full transition-all duration-500 ease-out"
                    :class="`bg-${getProgressColor(doc)}-500 dark:bg-${getProgressColor(doc)}-600`"
                    :style="`width: ${getApprovalProgress(doc)}%`"
                ></div>
              </div>

              <div class="p-5">
                <div class="flex flex-col sm:flex-row justify-between sm:items-start gap-4">
                  <div>
                    <div class="flex flex-wrap items-center gap-2 mb-2">
                      <UBadge
                          :color="getPriorityInfo(doc.priority).color"
                          size="sm"
                          :label="getPriorityInfo(doc.priority).name"
                          class="font-medium"
                      />
                      <UBadge
                          v-if="isOverdue(doc.dueDate)"
                          color="error"
                          size="sm"
                          label="Прострочено"
                          class="font-medium"
                      />
                      <span class="text-sm font-medium text-gray-500 dark:text-gray-400">{{ doc.number }}</span>
                    </div>
                    <h3 class="text-xl font-bold text-gray-900 dark:text-white mb-3 line-clamp-1">{{ doc.name }}</h3>
                    <div class="text-sm text-gray-600 dark:text-gray-400 flex flex-wrap gap-x-4 gap-y-2">
                      <div class="flex items-center">
                        <UIcon name="i-heroicons-document-text" class="mr-1.5 text-gray-400 dark:text-gray-500" />
                        <span>{{ getDocumentTypeName(doc.type) }}</span>
                      </div>
                      <div class="flex items-center">
                        <UIcon name="i-heroicons-user" class="mr-1.5 text-gray-400 dark:text-gray-500" />
                        <span>{{ doc.author }}</span>
                      </div>
                      <div class="flex items-center">
                        <UIcon name="i-heroicons-building-office-2" class="mr-1.5 text-gray-400 dark:text-gray-500" />
                        <span>{{ doc.department }}</span>
                      </div>
                      <div class="flex items-center">
                        <UIcon name="i-heroicons-calendar" class="mr-1.5 text-gray-400 dark:text-gray-500" />
                        <span :class="{ 'text-rose-600 dark:text-rose-400 font-medium': isOverdue(doc.dueDate) }">
                          {{ formatDate(doc.dueDate) }}
                        </span>
                      </div>
                    </div>
                  </div>

                  <UButton
                      color="primary"
                      variant="soft"
                      @click="viewDocument(doc)"
                      class="sm:self-start font-medium hover:bg-primary-100 dark:hover:bg-primary-900/40 transition-colors duration-200 rounded-lg"
                  >
                    Переглянути
                  </UButton>
                </div>

                <!-- Approval stages -->
                <div class="mt-5 pt-5 border-t border-gray-100 dark:border-gray-700">
                  <div class="mb-4">
                    <div class="flex justify-between items-center mb-1.5">
                      <h4 class="font-medium text-gray-900 dark:text-white">Прогрес погодження</h4>
                      <div class="text-sm font-medium text-gray-600 dark:text-gray-400">
                        {{ getApprovalProgress(doc) }}% виконано
                      </div>
                    </div>

                    <!-- Stage visualization -->
                    <div class="relative pt-4">
                      <div class="absolute top-0 left-0 w-full h-0.5 bg-gray-200 dark:bg-gray-700"></div>
                      <div class="flex justify-between">
                        <template v-for="stage in doc.totalStages" :key="stage">
                          <div class="relative flex flex-col items-center">
                            <div
                                class="w-6 h-6 rounded-full flex items-center justify-center text-xs font-bold text-white -mt-3"
                                :class="{
                                'bg-emerald-500 dark:bg-emerald-600': stage < doc.approvalStage,
                                'bg-amber-500 dark:bg-amber-600': stage === doc.approvalStage,
                                'bg-gray-300 dark:bg-gray-600': stage > doc.approvalStage
                              }"
                            >
                              {{ stage }}
                            </div>
                            <span class="text-xs font-medium mt-1.5">Етап {{ stage }}</span>
                          </div>
                        </template>
                      </div>
                    </div>
                  </div>

                  <!-- Current approvers -->
                  <div v-if="doc.approvers && doc.approvers.length > 0" class="mt-4">
                    <h5 class="text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">Поточні погоджувачі</h5>
                    <div class="flex flex-wrap gap-2">
                      <div
                          v-for="approver in doc.approvers.filter(a => a.stage === doc.approvalStage)"
                          :key="approver.approverId"
                          class="inline-flex items-center px-3 py-1.5 rounded-lg text-sm"
                          :class="{
                          'bg-emerald-50 dark:bg-emerald-900/20 text-emerald-700 dark:text-emerald-300': approver.approved === true && !approver.withRemarks,
                          'bg-amber-50 dark:bg-amber-900/20 text-amber-700 dark:text-amber-300': approver.approved === true && approver.withRemarks,
                          'bg-rose-50 dark:bg-rose-900/20 text-rose-700 dark:text-rose-300': approver.approved === false,
                          'bg-gray-50 dark:bg-gray-800 text-gray-700 dark:text-gray-300 border border-gray-200 dark:border-gray-700': approver.approved === null,
                          'ring-2 ring-amber-300 dark:ring-amber-500 animate-pulse': approver.approverId === CURRENT_USER_ID && approver.approved === null
                        }"
                      >
                        <span class="font-medium">{{ approver.name }}</span>
                        <UBadge v-if="approver.approverId === CURRENT_USER_ID" size="xs" color="primary" class="ml-1.5" label="Ви" />
                      </div>
                    </div>
                  </div>

                  <!-- Approval button if user can approve -->
                  <div v-if="canApprove(doc)" class="mt-4">
                    <UButton
                        color="success"
                        variant="solid"
                        class="w-full font-medium shadow-sm hover:shadow transition-all duration-200 rounded-lg"
                        @click="openDocument(doc)"
                    >
                      Погодити документ
                    </UButton>
                  </div>
                </div>
              </div>
            </div>

            <UAlert
                v-if="filteredDocuments.length === 0"
                color="primary"
                variant="soft"
                title="Немає документів для погодження"
                description="Документи, які очікують вашого погодження, з'являться тут."
                icon="i-heroicons-information-circle"
                class="rounded-xl"
            />
          </div>
        </template>

        <template #history>
          <!-- Approval history -->
          <div class="bg-white dark:bg-gray-800 rounded-xl shadow-sm p-5 overflow-hidden">
            <h2 class="text-xl font-bold mb-4 text-gray-900 dark:text-white">Історія погоджень</h2>

            <div class="relative overflow-x-auto">
              <table class="min-w-full table-fixed border-collapse">
                <thead class="bg-gray-50 dark:bg-gray-800/50">
                <tr>
                  <th class="text-left px-4 py-3.5 text-gray-900 dark:text-white font-medium text-sm">Номер</th>
                  <th class="text-left px-4 py-3.5 text-gray-900 dark:text-white font-medium text-sm">Назва</th>
                  <th class="text-left px-4 py-3.5 text-gray-900 dark:text-white font-medium text-sm">Погоджувач</th>
                  <th class="text-left px-4 py-3.5 text-gray-900 dark:text-white font-medium text-sm">Посада</th>
                  <th class="text-left px-4 py-3.5 text-gray-900 dark:text-white font-medium text-sm">Рішення</th>
                  <th class="text-left px-4 py-3.5 text-gray-900 dark:text-white font-medium text-sm">Дата</th>
                  <th class="text-left px-4 py-3.5 text-gray-900 dark:text-white font-medium text-sm">Коментар</th>
                </tr>
                </thead>
                <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
                <tr
                    v-for="item in sortedApprovalHistory"
                    :key="item.id"
                    class="hover:bg-gray-50 dark:hover:bg-gray-800/70 transition-colors duration-200"
                >
                  <td class="px-4 py-3 text-sm text-gray-600 dark:text-gray-300">{{ item.documentNumber }}</td>
                  <td class="px-4 py-3 text-sm text-gray-600 dark:text-gray-300">{{ item.documentName }}</td>
                  <td class="px-4 py-3 text-sm text-gray-600 dark:text-gray-300">{{ item.approvedBy }}</td>
                  <td class="px-4 py-3 text-sm text-gray-600 dark:text-gray-300">{{ item.position }}</td>
                  <td class="px-4 py-3 text-sm text-gray-600 dark:text-gray-300">
                    <UBadge
                        v-if="item.approved && !item.withRemarks"
                        color="success"
                        variant="subtle"
                        label="Погоджено"
                        class="font-medium"
                    />
                    <UBadge
                        v-else-if="item.approved && item.withRemarks"
                        color="warning"
                        variant="subtle"
                        label="Із зауваженнями"
                        class="font-medium"
                    />
                    <UBadge
                        v-else
                        color="error"
                        variant="subtle"
                        label="Відхилено"
                        class="font-medium"
                    />
                  </td>
                  <td class="px-4 py-3 text-sm text-gray-600 dark:text-gray-300">{{ formatDate(item.approvedOn) }}</td>
                  <td class="px-4 py-3 text-sm text-gray-600 dark:text-gray-300">
                    <div class="max-w-xs truncate">{{ item.comment }}</div>
                  </td>
                </tr>
                <tr v-if="sortedApprovalHistory.length === 0">
                  <td colspan="7" class="px-4 py-6 text-center text-sm text-gray-500 dark:text-gray-400">
                    Немає даних для відображення
                  </td>
                </tr>
                </tbody>
              </table>
            </div>
          </div>
        </template>
      </UTabs>
    </div>

    <!-- Document view modal -->
    <div v-if="isDocumentModalOpen" id="document-view" class="document-view-card">
      <UCard v-if="currentDocument" :ui="{ root: 'shadow-xl rounded-xl overflow-hidden border-0', body: 'p-0' }">
        <template #header>
          <div class="bg-gradient-to-r from-gray-50 to-white dark:from-gray-800 dark:to-gray-900 py-4 px-6">
            <div class="flex justify-between items-start">
              <div>
                <div class="flex items-center gap-2">
                  <UBadge
                      :color="getPriorityInfo(currentDocument.priority).color"
                      size="sm"
                      :label="getPriorityInfo(currentDocument.priority).name"
                      class="font-medium"
                  />
                  <span class="text-sm font-medium text-gray-500 dark:text-gray-400">{{ currentDocument.number }}</span>
                </div>
                <h3 class="text-2xl font-bold mt-1 text-gray-900 dark:text-white">{{ currentDocument.name }}</h3>
              </div>
              <UButton
                  color="neutral"
                  variant="ghost"
                  icon="i-heroicons-x-mark"
                  class="-my-1 hover:bg-gray-100 dark:hover:bg-gray-800 transition-colors duration-200"
                  @click="isDocumentModalOpen = false"
              />
            </div>
          </div>
        </template>

        <div class="p-0">
          <div class="p-6 space-y-6">
            <!-- Approval progress -->
            <div class="bg-gray-50 dark:bg-gray-800/70 rounded-xl p-5">
              <div class="flex justify-between items-center mb-3">
                <h4 class="font-bold text-gray-900 dark:text-white">Прогрес погодження</h4>
                <div class="text-sm font-medium text-gray-600 dark:text-gray-400 bg-white dark:bg-gray-800 py-1 px-2.5 rounded-full">
                  {{ getApprovalProgress(currentDocument) }}% завершено
                </div>
              </div>

              <div class="h-2.5 bg-gray-200 dark:bg-gray-700 rounded-full overflow-hidden mb-5">
                <div
                    class="h-full rounded-full transition-all duration-500 ease-out"
                    :class="`bg-${getProgressColor(currentDocument)}-500 dark:bg-${getProgressColor(currentDocument)}-600`"
                    :style="`width: ${getApprovalProgress(currentDocument)}%`"
                ></div>
              </div>

              <!-- Approval stages -->
              <div class="relative">
                <div class="absolute top-4 left-0 w-full h-0.5 bg-gray-200 dark:bg-gray-700"></div>
                <div class="relative flex justify-between">
                  <template v-for="stage in currentDocument.totalStages" :key="stage">
                    <div class="flex flex-col items-center">
                      <div
                          class="w-8 h-8 rounded-full flex items-center justify-center text-white font-bold mb-2 z-10"
                          :class="{
                          'bg-emerald-500 dark:bg-emerald-600': stage < currentDocument.approvalStage,
                          'bg-amber-500 dark:bg-amber-600': stage === currentDocument.approvalStage,
                          'bg-gray-300 dark:bg-gray-600': stage > currentDocument.approvalStage
                        }"
                      >
                        {{ stage }}
                      </div>
                      <span class="text-xs font-medium">Етап {{ stage }}</span>
                    </div>
                  </template>
                </div>
              </div>
            </div>

            <!-- Document info -->
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-5 bg-white dark:bg-gray-900 rounded-xl p-5 border border-gray-100 dark:border-gray-800">
              <div>
                <p class="text-sm font-medium text-gray-500 dark:text-gray-400">Тип документа</p>
                <p class="font-medium text-gray-900 dark:text-white">{{ getDocumentTypeName(currentDocument.type) }}</p>
              </div>
              <div>
                <p class="text-sm font-medium text-gray-500 dark:text-gray-400">Автор</p>
                <p class="font-medium text-gray-900 dark:text-white">{{ currentDocument.author }}</p>
              </div>
              <div>
                <p class="text-sm font-medium text-gray-500 dark:text-gray-400">Відділ</p>
                <p class="font-medium text-gray-900 dark:text-white">{{ currentDocument.department }}</p>
              </div>
              <div>
                <p class="text-sm font-medium text-gray-500 dark:text-gray-400">Термін погодження</p>
                <p
                    class="font-medium"
                    :class="{ 'text-rose-600 dark:text-rose-400': isOverdue(currentDocument.dueDate) }"
                >
                  {{ formatDate(currentDocument.dueDate) }}
                </p>
              </div>
              <div>
                <p class="text-sm font-medium text-gray-500 dark:text-gray-400">Дата створення</p>
                <p class="font-medium text-gray-900 dark:text-white">{{ formatDate(currentDocument.createdOn) }}</p>
              </div>
              <div>
                <p class="text-sm font-medium text-gray-500 dark:text-gray-400">Дата зміни</p>
                <p class="font-medium text-gray-900 dark:text-white">{{ formatDate(currentDocument.modifiedOn) }}</p>
              </div>
            </div>

            <!-- Document content (placeholder) -->
            <div class="rounded-xl p-5 bg-gray-50 dark:bg-gray-800/50 h-64 overflow-auto border border-gray-100 dark:border-gray-700">
              <div class="flex items-center justify-center h-full">
                <div class="text-center">
                  <UIcon name="i-heroicons-document" class="w-12 h-12 mx-auto mb-3 text-gray-400 dark:text-gray-500" />
                  <p class="text-sm text-gray-600 dark:text-gray-400">
                    Вміст документа. В реальному додатку тут буде відображатися вміст документа або вбудований PDF-переглядач.
                  </p>
                </div>
              </div>
            </div>

            <!-- Approvers -->
            <div>
              <h4 class="font-bold text-gray-900 dark:text-white mb-4">Погоджувачі</h4>
              <div class="space-y-3">
                <div
                    v-for="approver in currentDocument.approvers"
                    :key="approver.approverId"
                    class="flex items-center justify-between p-4 rounded-xl"
                    :class="{
                    'bg-emerald-50 dark:bg-emerald-900/20 border border-emerald-100 dark:border-emerald-800/50': approver.approved === true && !approver.withRemarks,
                    'bg-amber-50 dark:bg-amber-900/20 border border-amber-100 dark:border-amber-800/50': approver.approved === true && approver.withRemarks,
                    'bg-rose-50 dark:bg-rose-900/20 border border-rose-100 dark:border-rose-800/50': approver.approved === false,
                    'bg-white dark:bg-gray-800 border border-gray-100 dark:border-gray-700': approver.approved === null,
                    'ring-2 ring-amber-300 dark:ring-amber-500': approver.stage === currentDocument.approvalStage && approver.approved === null && approver.approverId === CURRENT_USER_ID
                  }"
                >
                  <div class="flex items-center">
                    <div class="w-10 h-10 flex items-center justify-center rounded-full mr-4 font-bold text-white shadow-sm"
                         :class="{
                        'bg-emerald-500 dark:bg-emerald-600': approver.approved === true && !approver.withRemarks,
                        'bg-amber-500 dark:bg-amber-600': approver.approved === true && approver.withRemarks,
                        'bg-rose-500 dark:bg-rose-600': approver.approved === false,
                        'bg-gray-300 dark:bg-gray-600': approver.approved === null
                      }"
                    >
                      {{ approver.stage }}
                    </div>
                    <div>
                      <div class="font-bold text-gray-900 dark:text-white flex items-center">
                        {{ approver.name }}
                        <UBadge v-if="approver.approverId === CURRENT_USER_ID" size="xs" color="primary" class="ml-1.5" label="Ви" />
                      </div>
                      <div class="text-sm text-gray-600 dark:text-gray-400">{{ approver.position }}</div>
                      <div v-if="approver.comment" class="text-sm italic mt-2 bg-white dark:bg-gray-800 p-2 rounded-lg border border-gray-100 dark:border-gray-700">
                        {{ approver.comment }}
                      </div>
                    </div>
                  </div>

                  <div class="flex items-center">
                    <div v-if="approver.approved !== null" class="text-sm mr-3 font-medium text-right">
                      {{ formatDate(approver.approvedOn || null) }}
                    </div>

                    <UBadge
                        v-if="approver.approved === true && !approver.withRemarks"
                        color="success"
                        variant="solid"
                        label="Погоджено"
                        class="font-medium"
                    />
                    <UBadge
                        v-else-if="approver.approved === true && approver.withRemarks"
                        color="warning"
                        variant="solid"
                        label="Із зауваженнями"
                        class="font-medium"
                    />
                    <UBadge
                        v-else-if="approver.approved === false"
                        color="error"
                        variant="solid"
                        label="Відхилено"
                        class="font-medium"
                    />
                    <UBadge
                        v-else
                        color="neutral"
                        variant="solid"
                        label="Очікує"
                        class="font-medium"
                    />
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Approval Section UI -->
          <div v-if="canApprove(currentDocument)" class="border-t border-gray-100 dark:border-gray-800 mt-4">
            <div class="p-6">
              <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-4 mb-6">
                <div>
                  <h4 class="font-bold text-gray-900 dark:text-white text-lg flex items-center gap-2">
                    <UIcon name="i-heroicons-hand-thumb-up" class="text-success-500 w-6 h-6" />
                    Ваше рішення щодо документа
                  </h4>
                  <p class="text-gray-500 dark:text-gray-400 mt-1 text-sm">
                    Оберіть дію або делегуйте погодження іншому співробітнику.
                  </p>
                </div>
                <UButton
                    v-if="!showDelegateForm"
                    color="primary"
                    variant="soft"
                    size="md"
                    icon="i-heroicons-user-plus"
                    @click="showDelegateForm = true"
                    class="font-medium"
                >
                  Делегувати погодження
                </UButton>
                <UButton
                    v-else
                    color="neutral"
                    variant="soft"
                    size="md"
                    icon="i-heroicons-arrow-uturn-left"
                    @click="showDelegateForm = false"
                    class="font-medium"
                >
                  Скасувати делегування
                </UButton>
              </div>

              <!-- Delegation form -->
              <div v-if="showDelegateForm" class="mb-6 p-6 rounded-2xl bg-blue-50 dark:bg-blue-900/20 border border-blue-100 dark:border-blue-800/50 shadow-sm">
                <div class="flex items-center gap-3 mb-3">
                  <UIcon name="i-heroicons-user-arrow-right" class="text-blue-500 w-6 h-6" />
                  <h5 class="font-bold text-gray-900 dark:text-white text-base">Делегування погодження</h5>
                </div>
                <p class="text-sm text-gray-600 dark:text-gray-400 mb-4">
                  Оберіть співробітника, якому ви хочете делегувати погодження цього документа.
                </p>
                <UFormGroup label="Делегувати до" name="delegateTo">
                  <USelect
                      v-model="delegateToUser"
                      :options="filteredApprovers"
                      option-attribute="name"
                      value-attribute="id"
                      placeholder="Оберіть співробітника"
                      class="focus-within:ring-2 focus-within:ring-blue-500 dark:focus-within:ring-blue-400 transition-shadow duration-200"
                  />
                </UFormGroup>
                <div class="flex justify-end mt-6">
                  <UButton
                      color="primary"
                      size="lg"
                      :disabled="!delegateToUser"
                      icon="i-heroicons-user-arrow-right"
                      @click="delegateApproval"
                      class="font-semibold shadow-sm hover:shadow transition-all duration-200 px-8"
                  >
                    Делегувати
                  </UButton>
                </div>
              </div>

              <!-- Approval form -->
              <div v-else>
                <UFormGroup label="Коментар (необов'язково)" name="comment">
                  <UTextarea
                      v-model="approvalComment"
                      placeholder="Введіть коментар або зауваження до документа"
                      :rows="3"
                      class="focus-within:ring-2 focus-within:ring-primary-500 dark:focus-within:ring-primary-400 transition-shadow duration-200"
                  />
                </UFormGroup>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mt-6">
                  <UButton
                      color="success"
                      variant="solid"
                      block
                      size="lg"
                      icon="i-heroicons-check-circle"
                      @click="approvalDecision = 'approve'; approveDocument()"
                      class="font-semibold shadow-sm hover:shadow transition-all duration-200"
                  >
                    Погодити
                  </UButton>
                  <UButton
                      color="warning"
                      variant="solid"
                      block
                      size="lg"
                      icon="i-heroicons-exclamation-circle"
                      @click="approvalDecision = 'approve_with_remarks'; approveDocument()"
                      class="font-semibold shadow-sm hover:shadow transition-all duration-200"
                  >
                    Із зауваженнями
                  </UButton>
                  <UButton
                      color="error"
                      variant="solid"
                      block
                      size="lg"
                      icon="i-heroicons-x-circle"
                      @click="approvalDecision = 'reject'; approveDocument()"
                      class="font-semibold shadow-sm hover:shadow transition-all duration-200"
                  >
                    Відхилити
                  </UButton>
                </div>
              </div>
            </div>
          </div>
        </div>
      </UCard>
    </div>
  </div>
</template>

<style scoped>
/* Animation for current user's pending approvals */
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.7; }
}

.animate-pulse {
  animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}

/* Smooth transitions */
.transition-all {
  transition-property: all;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 200ms;
}

/* Focus styles for better accessibility */
*:focus-visible {
  outline: 2px solid rgb(var(--color-primary-500));
  outline-offset: 2px;
}

/* Document view card styles */
.document-view-card {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 50;
  max-width: 90vw;
  width: 900px;
  max-height: 90vh;
  overflow-y: auto;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 4px 24px rgba(0,0,0,0.15);
  animation: modalAppear 0.3s ease-out;
}

@keyframes modalAppear {
  from { opacity: 0; transform: translate(-50%, -48%); }
  to { opacity: 1; transform: translate(-50%, -50%); }
}

/* Dark mode styles for the modal */
.dark .document-view-card {
  background: #1f2937;
  color: #e5e7eb;
}
</style>