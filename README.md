<div class="w-full overflow-x-auto">
  <table class="min-w-full border-collapse border border-gray-200">
    <thead>
      <tr class="bg-gray-100">
        <th class="border border-gray-300 px-4 py-2"></th>
        <th *ngFor="let item of headers" class="border border-gray-300 px-4 py-2 text-left">{{ item }}</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let row of data">
        <td class="border border-gray-300 px-4 py-2 font-bold">{{ row.label }}</td>
        <td *ngFor="let value of row.values" class="border border-gray-300 px-4 py-2">{{ value }}</td>
      </tr>
    </tbody>
  </table>
</div>
