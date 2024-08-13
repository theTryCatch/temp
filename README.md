<div class="w-full overflow-x-auto">
  <table class="min-w-full border-separate border border-gray-300 rounded-lg shadow-md">
    <thead>
      <tr class="bg-blue-500 text-white">
        <th class="border-b border-gray-300 px-4 py-2 text-left rounded-tl-lg"></th>
        <th *ngFor="let header of headers" class="border-b border-gray-300 px-4 py-2 text-left">{{ header }}</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let row of data">
        <td class="border-b border-gray-300 px-4 py-2 font-medium bg-gray-100">{{ row.label }}</td>
        <td *ngFor="let value of row.values" class="border-b border-gray-300 px-4 py-2">{{ value }}</td>
      </tr>
    </tbody>
  </table>
</div>
