<template>
  <section>
    <div class="max-w-2xl p-6 mx-auto bg-white">
      <!-- Navigation and Breadcrumbs -->
      <div class="text-sm text-gray-500 flex items-center">
        <svg
          @click="router.back()"
          class="cursor-pointer mb-5 mr-4"
          width="36"
          height="36"
          viewBox="0 0 36 36"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <rect width="36" height="36" rx="18" fill="#EAEAEA" />
          <path
            d="M20.5 13C20.5 13 15.5 16.6824 15.5 18C15.5 19.3177 20.5 23 20.5 23"
            stroke="#1D2739"
            stroke-width="1.5"
            stroke-linecap="round"
            stroke-linejoin="round"
          />
        </svg>
        <span class="-mt-6">{{title}}</span>
        <span class="mx-2 -mt-6">|</span>
        <span class="font-semibold text-[#1D2939] -mt-6">Generate Invoice</span>
      </div>

      <!-- Generate Invoice Step -->
      <div v-if="activeStep === 'generate'">
        <h2 class="mb-4 text-base font-medium">Details</h2>
        <form @submit.prevent="handleSubmit" class="space-y-4">
          <!-- Form Fields -->
          <div class="grid grid-cols-2 gap-4">
            <div>
              <label class="block font-medium text-[#1D2739] text-xs">Bill From</label>
              <input
                type="text"
                v-model="formData.billFrom"
                placeholder="Bill From"
                class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
              />
            </div>
            <div>
              <label class="block font-medium text-[#1D2739] text-xs">Bill To</label>
              <input
                type="text"
                v-model="formData.billTo"
                placeholder="Bill To"
                class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
              />
            </div>
          </div>
          <div>
            <label class="block font-medium text-[#1D2739] text-xs">Recipient Email</label>
            <input
              type="email"
              v-model="formData.recipientEmail"
              placeholder="Recipient Email"
              class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
            />
          </div>
          <div>
            <label class="block font-medium text-[#1D2739] text-xs">Bill Title</label>
            <input
              type="text"
              v-model="formData.billTitle"
              placeholder="Bill Title"
              class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
            />
          </div>
          <div class="grid grid-cols-2 gap-4">
            <div>
              <label class="block font-medium text-[#1D2739] text-xs">Issued on</label>
              <input
                type="date"
                v-model="formData.issuedOn"
                class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
              />
            </div>
            <div>
              <label class="block font-medium text-[#1D2739] text-xs">Due on</label>
              <input
                type="date"
                v-model="formData.dueOn"
                class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
              />
            </div>
          </div>

          <!-- Invoice Item Section -->
          <h2 class="text-base font-semibold text-[#1D2739]">Invoice Item</h2>
          <div
            v-for="(item, index) in items"
            :key="index"
            class="grid grid-cols-1 md:grid-cols-4 gap-2 mb-4 relative"
          >
            <div>
              <label class="block text-xs font-medium text-gray-700">Item</label>
              <input
                type="text"
                v-model="item.name"
                placeholder="Enter item"
                class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
              />
            </div>
            <div>
              <label class="block text-sm font-medium text-[#1D2739] text-xs">Price (£)</label>
              <input
                type="number"
                v-model.number="item.price"
                placeholder="e.g 1000"
                class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
              />
            </div>
            <div>
              <label class="block text-sm font-medium text-[#1D2739] text-xs">Quantity</label>
              <input
                type="number"
                v-model.number="item.quantity"
                placeholder="e.g 2"
                class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
              />
            </div>
            <div>
              <label class="block text- font-medium text-[#1D2739] text-xs">Total Price (£)</label>
              <input
                type="text"
                :value="(item.price * item.quantity).toFixed(2)"
                disabled
                class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
              />
            </div>
            <!-- Delete Button -->
            <div class="absolute right-0 -top-6 lg:-right-9 lg:top-5 mt-4 flex space-x-2">
              <button @click="removeItem(index)" class="text-red-600 hover:text-red-800 transition">
                <!-- SVG Delete Icon -->
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="19"
                  height="19"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="#d0021b"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <polyline points="3 6 5 6 21 6"></polyline>
                  <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                  <line x1="10" y1="11" x2="10" y2="17"></line>
                  <line x1="14" y1="11" x2="14" y2="17"></line>
                </svg>
              </button>
            </div>
          </div>

          <!-- Add Item Button and Total Amount -->
          <div class="flex items-center justify-between space-x-2 pt-4">
            <button
              @click="addItem"
              :disabled="!canAddItem"
              class="text-gray-800 text-sm font-medium flex items-center space-x-1 hover:text-gray-600 transition disabled:opacity-50 disabled:cursor-not-allowed"
            >
              <span class="text-xl">+</span>
              <span>Add Item</span>
            </button>
            <span class="text-sm font-medium">Total Amount</span>
            <span class="text- font-medium text-sm text-gray-600">{{ totalAmount.toFixed(2) }}</span>
          </div>

          <!-- Note Section -->
          <h2 class="mb-2 text-base font-semibold">Note</h2>
          <div>
            <label class="block font-medium text-[#1D2739] text-xs">Drop a note</label>
            <textarea
              v-model="formData.note"
              rows="3"
              cols="3"
              placeholder="Enter your reason for moving out."
              class="w-full p-2 mt-1 outline-none focus-within:border-2 focus-within:border-[#5B8469] border-[0.5px] text-sm rounded-md bg-[#E4E7EC] py-4"
            ></textarea>
          </div>

          <!-- Form Navigation Buttons -->
          <div class="flex justify-between items-center gap-4 pt-10">
            <button @click="router.push('/dashboard/invoice')" type="button" class="p-2 px-6 py-3 bg-gray-200 rounded-md">
              Cancel
            </button>
            <button @click="proceedToPreview" type="button" class="p-2 px-6 py-3 text-white bg-[#292929] rounded-md">
              Continue
            </button>
          </div>
        </form>
      </div>

      <!-- Preview Step -->
      <div v-if="activeStep === 'preview'">
        <InvoiceReview :formData="formData" :items="items" :totalAmount="totalAmount" />
        <div class="flex justify-between items-center gap-4 pt-10">
          <button @click="updateURL('generate')" type="button" class="p-2 px-6 py-3 bg-gray-200 rounded-md">
            Back
          </button>
          <button @click="router.push('/dashboard/invoice/success')" type="button" class="p-2 px-6 py-3 text-white bg-[#292929] rounded-md">
            Confirm
          </button>
        </div>
      </div>
    </div>
  </section>
</template>

<script lang="ts" setup>
import { ref, computed, onMounted, watch } from 'vue';
import { useRouter, useRoute } from 'vue-router';

const router = useRouter();
const route = useRoute();

const activeStep = ref('generate');

// Define structure for formData and items
const formData = ref({
  billFrom: '',
  billTo: '',
  recipientEmail: '',
  billTitle: '',
  issuedOn: '',
  dueOn: '',
  note: ''
});

const props = defineProps({
  title: {
    type: String
  },
})

interface InvoiceItem {
  name: string;
  price: number;
  quantity: number;
}

const items = ref<InvoiceItem[]>([{ name: '', price: 0, quantity: 0 }]);

const addItem = () => {
  items.value.push({ name: '', price: 0, quantity: 0 });
};

const removeItem = (index: number) => {
  items.value.splice(index, 1);
};

const canAddItem = computed(() => items.value.every(item => item.name && item.price > 0 && item.quantity > 0));

const totalAmount = computed(() => items.value.reduce((sum, item) => sum + item.price * item.quantity, 0));

// Set the initial activeStep to 'generate' on page mount
onMounted(() => {
  if (route.query.step !== 'generate') {
    updateURL('generate');
  }
});

// Update URL whenever activeStep changes
watch(activeStep, (newStep) => {
  updateURL(newStep);
});

const updateURL = (step: string) => {
  activeStep.value = step;
  router.push({ query: { ...route.query, step } });
};

// Function to proceed to preview step
const proceedToPreview = () => {
  updateURL('preview');
};

const emit = defineEmits(['success'])

const handleSubmit = () => {
  emit('success')
};
</script>
