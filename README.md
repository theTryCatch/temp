import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'sortByProperty'
})
export class SortByPropertyPipe implements PipeTransform {
  transform(array: any[], property: string): any[] {
    if (!Array.isArray(array)) {
      return [];
    }

    return array.slice().sort((a, b) => {
      const valueA = a[property] ? a[property].toString().toLowerCase() : '';
      const valueB = b[property] ? b[property].toString().toLowerCase() : '';
      return valueA.localeCompare(valueB);
    }).map(item => item[property]);
  }
}
