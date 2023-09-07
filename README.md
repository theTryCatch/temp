import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'sortByProperty'
})
export class SortByPropertyPipe implements PipeTransform {
  transform(array: any[], property: string): any[] {
    if (!Array.isArray(array)) {
      return [];
    }

    const validItems: any[] = [];

    // Filter out items where the property is missing or undefined
    for (const item of array) {
      if (item[property] !== undefined) {
        validItems.push(item);
      }
    }

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
