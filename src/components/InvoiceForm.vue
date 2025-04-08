<template>
  <div class="max-w-6xl mx-auto">
    <div class="flex justify-end mb-4">
      <button
        @click="generatePDF"
        class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg text-sm"
      >
        Download PDF
      </button>
    </div>

    <!-- Regular UI Form (same as yours) -->
    <!-- Replace with your current editable layout as needed -->

    <div class="max-w-5xl p-6 bg-white rounded-xl shadow-md border text-blue-900">
      <div class="flex items-center justify-between mb-6">
        <div class="flex items-center space-x-3">
          <!-- Vue logo SVG -->
          <img src="https://vuejs.org/images/logo.png" alt="Vue.js Logo" class="w-12 h-12" />
          <input v-model="companyName" class="text-3xl font-bold text-blue-800" /><span
            >{{ '<-' }}title editable</span
          >
        </div>
      </div>
      <!-- Customer Info -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <input v-model="customer.name" placeholder="Customer Name" class="border p-2 rounded-md" />
        <input v-model="customer.phone" placeholder="Phone" class="border p-2 rounded-md" />
        <input v-model="customer.email" placeholder="Email" class="border p-2 rounded-md" />
        <textarea v-model="customer.address" placeholder="Address" class="border p-2 rounded-md" />
      </div>

      <!-- Invoice Meta -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-6">
        <input
          v-model="invoice.number"
          placeholder="Invoice Number"
          class="border p-2 rounded-md"
        />
        <input v-model="invoice.issueDate" type="date" class="border p-2 rounded-md" />
        <input v-model="invoice.dueDate" type="date" class="border p-2 rounded-md" />
      </div>

      <!-- Items -->
      <div class="mt-6">
        <h2 class="text-lg font-semibold">Items</h2>
        <div v-for="(item, index) in items" :key="index" class="grid grid-cols-4 gap-2 mt-2">
          <input
            v-model="item.description"
            placeholder="Description"
            class="border p-2 rounded-md"
          />
          <input
            v-model.number="item.qty"
            type="number"
            placeholder="Qty"
            class="border p-2 rounded-md"
          />
          <input
            v-model.number="item.unitPrice"
            type="number"
            placeholder="Unit Price"
            class="border p-2 rounded-md"
          />
          <button @click="removeItem(index)" class="text-red-600">Remove</button>
        </div>
        <button
          @click="addItem"
          class="mt-3 px-4 py-2 text-sm bg-blue-100 text-blue-800 rounded-md"
        >
          + Add Item
        </button>
      </div>
    </div>
    <div class="mt-8 max-w-3xl">
      <label class="block text-sm font-semibold text-blue-800 mb-1">Terms & Conditions</label>
      <textarea
        v-model="terms"
        rows="8"
        class="w-full p-3 border rounded-md text-sm bg-blue-50 focus:outline-none focus:ring-2 focus:ring-blue-200 text-blue-900"
      ></textarea>
    </div>
    <div class="mt-8 max-w-3xl">
      <label class="block text-sm font-semibold text-blue-800 mb-1">Company Generated Text</label>
      <input
        v-model="compGenerated"
        rows="2"
        class="w-full p-3 border rounded-md text-sm bg-blue-50 focus:outline-none focus:ring-2 focus:ring-blue-200 text-blue-900"
      />
      <label class="block text-sm font-semibold text-blue-800 mb-1">Footer Text</label>
      <input
        v-model="footerText"
        rows="2"
        class="w-full p-3 border rounded-md text-sm bg-blue-50 focus:outline-none focus:ring-2 focus:ring-blue-200 text-blue-900"
      />
    </div>
  </div>
</template>

<script setup>
import { reactive, computed } from 'vue'
import { ref } from 'vue'
import pdfMake from 'pdfmake/build/pdfmake'
import * as pdfFonts from 'pdfmake/build/vfs_fonts'

pdfMake.vfs = pdfFonts.vfs

const customer = reactive({
  name: 'Edwin',
  phone: '+254712345678',
  email: 'eddimuhoro@gmail.com',
  address: 'Nairobi, Kenya',
})

const invoice = reactive({
  number: 'INV-001',
  issueDate: new Date().toISOString().slice(0, 10),
  dueDate: new Date().toISOString().slice(0, 10),
})

const items = reactive([
  { description: 'Returns Filing', qty: 1, unitPrice: 200 },
  { description: 'Tax Advice', qty: 1, unitPrice: 100 },
])

const terms =
  ref(`1. This invoice pertains to services rendered for filing tax returns with the Kenya Revenue Authority (KRA).
2. All information provided by the client is presumed accurate and complete. XYZ will not be held liable for any discrepancies or penalties arising from incorrect client-submitted data.
3. Confidentiality of client data is strictly maintained and used solely for the purpose of fulfilling tax return obligations.
4. For any clarifications or concerns, please contact us via email or phone within 3 business days of receiving this invoice.`)

const grandTotal = computed(() => items.reduce((sum, item) => sum + item.qty * item.unitPrice, 0))

const addItem = () => items.push({ description: '', qty: 1, unitPrice: 0 })
const removeItem = (index) => items.splice(index, 1)

const footerText = ref('Thank you for your business! We appreciate your trust in us.')
const compGenerated = ref('This invoice was generated by XYZ Tax Consultants. All rights reserved.')
const companyName = ref('XYZ Tax Consultants')
const logoBase64 =
  'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAHgAAAB4CAMAAAAOusbgAAAAeFBMVEX///8AAADx8fHk5OTU1NTb29ve3t7Y2NiwbQDJycmaDAD4+PjZ2dnr6+vR0dHf39/S0tLHx8eAgICoqKhLS0ujDgDl5eVoaGi0tLQjHx/Pz8+5ubkQExQqJiYZFhZISEhpYWA3MzObZJbaAAABrUlEQVRoge2Z2Y6DMAxFGUUtVFTVd77/X2DC5QptBGSuf79TGOmtKJRLD9FtyqGYV3qcf4JuMnDnueCAVTmlkG5dSu3Y0o5X4IehOEppN6FSxAgbIA7EkUq5fB5JKTx7ANqlMB7q9DsA6ke8RA8yNR9RNH2t9OZoN+AmXJm0RNBHJdjI3V9C7qUgQLN6o8MNP8oeu2yCH2OPXUQCcG9PBLKFPWjUtVaRit6tbsvMkhK7NdqZ3ThGGiM4SmjCeKFVFoMH1yNMLYSP+Kk8U1Q5aDXu7r9MJvRWRvyjXLbm3k2Xzff9C0NjWyCyay3z+eG4B13M0AY9aHAl4r+vEDOhnA/NtD5wAAAABJRU5ErkJggg=='

const generatePDF = () => {
  const docDefinition = {
    content: [
      // 🟦 Banner Title
      {
        columns: [
          // { image: logoBase64, width: 50, margin: [0, 0, 10, 0] },
          {
            stack: [
              { text: companyName.value, style: 'companyName', alignment: 'left' },
              { text: 'INVOICE', style: 'invoiceTitle', alignment: 'right' },
            ],
          },
        ],
        columnGap: 10,
        margin: [0, 0, 0, 20],
      },

      // 👤 Customer and Invoice Info
      {
        columns: [
          [
            { text: 'Billed To:', style: 'sectionHeader' },
            { text: customer.name, bold: true },
            { text: customer.phone },
            { text: customer.email },
            { text: customer.address },
          ],
          [
            { text: 'Invoice Details:', style: 'sectionHeader', alignment: 'right' },
            { text: `Invoice No: ${invoice.number}`, alignment: 'right' },
            { text: `Issue Date: ${invoice.issueDate}`, alignment: 'right' },
            { text: `Due Date: ${invoice.dueDate}`, alignment: 'right' },
          ],
        ],
        margin: [0, 0, 0, 30],
      },

      // 💵 Item Table
      {
        table: {
          headerRows: 1,
          widths: ['*', 'auto', 'auto', 'auto'],
          body: [
            [
              { text: 'Description', style: 'tableHeader' },
              { text: 'Qty', style: 'tableHeader', alignment: 'right' },
              { text: 'Unit Price', style: 'tableHeader', alignment: 'right' },
              { text: 'Total', style: 'tableHeader', alignment: 'right' },
            ],
            ...items.map((i) => [
              { text: i.description },
              { text: i.qty.toString(), alignment: 'right' },
              { text: `Ksh ${i.unitPrice.toFixed(2)}`, alignment: 'right' },
              { text: `Ksh ${(i.qty * i.unitPrice).toFixed(2)}`, alignment: 'right' },
            ]),
            [
              {
                text: 'Grand Total',
                colSpan: 3,
                alignment: 'right',
                bold: true,
                fillColor: '#f1f5f9',
              },
              {},
              {},
              {
                text: `Ksh ${grandTotal.value.toFixed(2)}`,
                alignment: 'right',
                bold: true,
                color: '#1e40af',
              },
            ],
          ],
        },
        layout: {
          hLineWidth: () => 0, // no horizontal lines
          vLineWidth: () => 0, // no vertical lines (optional)
          paddingTop: () => 8,
          paddingBottom: () => 8,
          fillColor: (rowIndex) => (rowIndex === 0 ? '#e0f2fe' : null),
        },
      },

      // 📜 Terms & Conditions
      {
        text: 'Terms & Conditions',
        style: 'sectionHeader',
        margin: [0, 30, 0, 6],
      },
      {
        text: terms.value || '',
        fontSize: 10,
        color: '#374151',
      },

      // 🙏 Footer
      {
        text: footerText.value,
        style: 'footerText',
        margin: [0, 30, 0, 0],
      },
      {
        text: compGenerated.value,
        style: 'footerNote',
      },
    ],

    styles: {
      sectionHeader: {
        fontSize: 13,
        bold: true,
        color: '#1e40af',
        margin: [0, 0, 0, 4],
      },
      tableHeader: {
        bold: true,
        fontSize: 12,
        color: '#1e40af',
      },
      footerText: {
        fontSize: 12,
        alignment: 'center',
        bold: true,
        color: '#1e3a8a',
      },
      footerNote: {
        fontSize: 9,
        alignment: 'center',
        color: '#6b7280',
      },
      companyName: {
        fontSize: 24,
        bold: true,
        color: '#1e3a8a',
      },
      invoiceTitle: {
        fontSize: 24,
        bold: true,
        color: '#1e3a8a',
        decoration: 'underline',
      },
    },

    defaultStyle: {
      fontSize: 11,
    },
  }

  pdfMake.createPdf(docDefinition).download(`invoice-${invoice.number}.pdf`)
}
</script>
