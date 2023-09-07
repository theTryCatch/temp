import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'sortByProperty'
})
export class SortByPropertyPipe implements PipeTransform {
  transform(array: any[], property: string): any[] {
    if (!Array.isArray(array)) {
      return [];
    }

    // Filter out items where the property is missing or undefined
    const validItems = array.filter(item => item[property] !== undefined);

    // Sort the valid items alphabetically
    const sortedItems = validItems.sort((a, b) => {
      const valueA = a[property].toString().toLowerCase();
      const valueB = b[property].toString().toLowerCase();
      return valueA.localeCompare(valueB);
    });

    // Extract and return the values of the specified property
    return sortedItems.map(item => item[property]);
  }
}
