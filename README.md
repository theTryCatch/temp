<div class="w-full overflow-x-auto">
  <table class="min-w-full border-collapse border border-gray-200">
    <thead>
      <tr class="bg-gray-100">
        <th class="border border-gray-300 px-4 py-2 text-left">Attribute</th>
        <th class="border border-gray-300 px-4 py-2 text-left">Value</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let item of data">
        <td class="border border-gray-300 px-4 py-2">{{ item.attribute }}</td>
        <td class="border border-gray-300 px-4 py-2">{{ item.value }}</td>
      </tr>
    </tbody>
  </table>
</div>
