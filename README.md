<div class="h-[800px] flex flex-col">
    <!-- Header -->
    <div class="flex justify-between items-center">
        <div class="min-h-12">
            <input
                type="text"
                [placeholder]="placeholderText"
                class="input input-bordered w-full max-w-xs mr-auto"
                [class.input-xs]="size === 'xs'"
                [class.input-sm]="size === 'sm'"
                [class.input-md]="size === 'md'"
                [class.input-lg]="size === 'lg'"
                [(ngModel)]="searchTerm"
                (input)="applyFilter()"
                *ngIf="hideSearchbox !== true"
            />
        </div>
        <label
            class="ml-auto"
            *ngIf="hideViewSelector !== true"
        >
            <label class="btn btn-circle swap swap-rotate">
                <!-- this hidden checkbox controls the state -->
                <input
                    type="checkbox"
                    [(ngModel)]="isVertical"
                    (change)="applyFilter()"
                />
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    height="24px"
                    viewBox="0 -960 960 960"
                    width="24px"
                    fill="#e8eaed"
                    class="swap-off fill-current"
                >
                    <path
                        d="M760-200v-120H200v120h560Zm0-200v-160H200v160h560Zm0-240v-120H200v120h560ZM200-120q-33 0-56.5-23.5T120-200v-560q0-33 23.5-56.5T200-840h560q33 0 56.5 23.5T840-760v560q0 33-23.5 56.5T760-120H200Z"
                    />
                </svg>
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    height="24px"
                    viewBox="0 -960 960 960"
                    width="24px"
                    fill="#e8eaed"
                    class="swap-on fill-current"
                >
                    <path
                        d="M280-600v-80h560v80H280Zm0 160v-80h560v80H280Zm0 160v-80h560v80H280ZM160-600q-17 0-28.5-11.5T120-640q0-17 11.5-28.5T160-680q17 0 28.5 11.5T200-640q0 17-11.5 28.5T160-600Zm0 160q-17 0-28.5-11.5T120-480q0-17 11.5-28.5T160-520q17 0 28.5 11.5T200-480q0 17-11.5 28.5T160-440Zm0 160q-17 0-28.5-11.5T120-320q0-17 11.5-28.5T160-360q17 0 28.5 11.5T200-320q0 17-11.5 28.5T160-280Z"
                    />
                </svg>
            </label>
        </label>
    </div>

    <!-- Table Container -->
    <div class="flex-1 overflow-auto">
        <table
            class="table table-pin-rows w-full min-w-full"
            [class.table-xs]="size === 'xs'"
            [class.table-sm]="size === 'sm'"
            [class.table-md]="size === 'md'"
            [class.table-lg]="size === 'lg'"
        >
            <!-- Head for horizontal layout -->
            <thead *ngIf="!isVertical">
                <tr class="hover">
                    <th
                        *ngFor="let key of displayedColumns"
                        (click)="sortData(key)"
                        [class.text-xs]="size === 'xs'"
                        [class.text-sm]="size === 'sm'"
                        [class.text-lg]="size === 'lg'"
                        class="text-gray-600"
                    >
                        {{ key }}
                        <span *ngIf="sortColumn === key">
                            {{ sortDirection === 'asc' ? '▲' : '▼' }}
                        </span>
                    </th>
                </tr>
            </thead>

            <!-- Body for horizontal layout -->
            <tbody
                *ngIf="!isVertical"
                [class.text-xs]="size === 'xs'"
                [class.text-sm]="size === 'sm'"
                [class.text-lg]="size === 'lg'"
            >
                <tr
                    *ngIf="filteredData.length === 0"
                    class="hover"
                >
                    <td
                        [attr.colspan]="displayedColumns.length"
                        class="text-center"
                    >
                        No records found
                    </td>
                </tr>
                <tr
                    *ngFor="let item of paginatedData"
                    class="hover"
                >
                    <td *ngFor="let key of displayedColumns">
                        <div *ngIf="key !== 'Actions'; else actions">
                            {{ item[key] !== undefined && item[key] !== null ? item[key] : 'N/A' }}
                        </div>
                        <ng-template #actions>
                            <div class="dropdown dropdown-bottom">
                                <div
                                    tabindex="0"
                                    role="button"
                                    class="m-0 p-0"
                                    [class.btn-xs]="size === 'xs'"
                                    [class.btn-sm]="size === 'sm'"
                                    [class.btn-md]="size === 'md'"
                                    [class.btn-lg]="size === 'lg'"
                                >
                                    <svg
                                        xmlns="http://www.w3.org/2000/svg"
                                        height="24px"
                                        viewBox="0 -960 960 960"
                                        width="24px"
                                        class="fill-neutral-700"
                                    >
                                        <path
                                            d="M240-400q-33 0-56.5-23.5T160-480q0-33 23.5-56.5T240-560q33 0 56.5 23.5T320-480q0 33-23.5 56.5T240-400Zm240 0q-33 0-56.5-23.5T400-480q0-33 23.5-56.5T480-560q33 0 56.5 23.5T560-480q0 33-23.5 56.5T480-400Zm240 0q-33 0-56.5-23.5T640-480q0-33 23.5-56.5T720-560q33 0 56.5 23.5T800-480q0 33-23.5 56.5T720-400Z"
                                        />
                                    </svg>
                                </div>
                                <ul
                                    tabindex="0"
                                    class="dropdown-content menu bg-base-100 rounded-box z-[1] w-52 p-2 shadow"
                                >
                                    <li><a>Item 1</a></li>
                                    <li><a>Item 2</a></li>
                                </ul>
                            </div>
                        </ng-template>
                    </td>
                </tr>
            </tbody>

            <!-- Body for vertical layout -->
            <tbody
                *ngIf="isVertical"
                [class.text-xs]="size === 'xs'"
                [class.text-sm]="size === 'sm'"
                [class.text-lg]="size === 'lg'"
            >
                <tr
                    *ngIf="filteredData.length === 0"
                    class="hover"
                >
                    <td
                        [attr.colspan]="displayedColumns.length"
                        class="text-center"
                    >
                        No records found
                    </td>
                </tr>
                <tr
                    *ngFor="let item of paginatedData"
                    class="hover border-0"
                >
                    <td>
                        <div class="grid grid-cols-1 gap-2">
                            <div *ngFor="let key of displayedColumns">
                                <table class="table w-full">
                                    <tr
                                        class="hover:bg-gray-500 table-row"
                                        [class.border-0]="noRowBorders === true"
                                    >
                                        <td style="width: 50%">{{key}}</td>
                                        <div *ngIf="key !== 'Actions'; else verticalActions">
                                            <td style="width: 50%">{{ item[key] !== undefined && item[key] !== null ?
                                                item[key] : 'N/A' }}</td>
                                        </div>
                                        <ng-template #verticalActions>
                                            <div class="dropdown dropdown-bottom">
                                                <div
                                                    tabindex="0"
                                                    role="button"
                                                    class="m-0 p-0"
                                                    [class.btn-xs]="size === 'xs'"
                                                    [class.btn-sm]="size === 'sm'"
                                                    [class.btn-md]="size === 'md'"
                                                    [class.btn-lg]="size === 'lg'"
                                                >
                                                    <svg
                                                        xmlns="http://www.w3.org/2000/svg"
                                                        height="24px"
                                                        viewBox="0 -960 960 960"
                                                        width="24px"
                                                        class="fill-neutral-700"
                                                    >
                                                        <path
                                                            d="M240-400q-33 0-56.5-23.5T160-480q0-33 23.5-56.5T240-560q33 0 56.5 23.5T320-480q0 33-23.5 56.5T240-400Zm240 0q-33 0-56.5-23.5T400-480q0-33 23.5-56.5T480-560q33 0 56.5 23.5T560-480q0 33-23.5 56.5T480-400Zm240 0q-33 0-56.5-23.5T640-480q0-33 23.5-56.5T720-560q33 0 56.5 23.5T800-480q0 33-23.5 56.5T720-400Z"
                                                        />
                                                    </svg>
                                                </div>
                                                <ul
                                                    tabindex="0"
                                                    class="dropdown-content menu bg-base-100 rounded-box z-[1] w-52 p-2 shadow"
                                                >
                                                    <li><a>Item 1</a></li>
                                                    <li><a>Item 2</a></li>
                                                </ul>
                                            </div>
                                        </ng-template>
                                    </tr>
                                </table>
                            </div>
                        </div>
                    </td>
                </tr>
                <!-- Add an empty row between records -->
                <tr *ngIf="paginatedData.length > 1">
                    <td [attr.colspan]="displayedColumns.length"></td>
                </tr>
            </tbody>
        </table>
    </div>

    <!-- Footer -->
    <div
        class="border-[1px] border-solid flex justify-between items-center"
        [class.text-xs]="size === 'xs'"
        [class.text-sm]="size === 'sm'"
        [class.text-md]="size === 'md'"
        [class.text-lg]="size === 'lg'"
        *ngIf="hidePaginator !== true"
    >
        <div>
            <label class="mr-2">Rows per page:</label>
            <select
                class="select select-bordered"
                [class.select-xs]="size === 'xs'"
                [class.select-sm]="size === 'sm'"
                [class.select-lg]="size === 'lg'"
                [(ngModel)]="itemsPerPage"
                (change)="onItemsPerPageChange()"
            >
                <option
                    *ngFor="let option of paginationOptions"
                    [value]="option"
                >{{ option }}</option>
            </select>
        </div>
        <div class="join">
            <button
                class="join-item btn"
                [class.btn-xs]="size === 'xs'"
                [class.btn-sm]="size === 'sm'"
                [class.btn-md]="size === 'md'"
                [class.btn-lg]="size === 'lg'"
                [class.btn-disabled]="page === 1"
                (click)="previousPage()"
            >
                «
            </button>
            <button
                class="join-item btn"
                [class.btn-xs]="size === 'xs'"
                [class.btn-sm]="size === 'sm'"
                [class.btn-md]="size === 'md'"
                [class.btn-lg]="size === 'lg'"
            >Page {{ page }} of {{ totalPages }}</button>
            <button
                class="join-item btn"
                [class.btn-xs]="size === 'xs'"
                [class.btn-sm]="size === 'sm'"
                [class.btn-md]="size === 'md'"
                [class.btn-lg]="size === 'lg'"
                [class.btn-disabled]="page === totalPages"
                (click)="nextPage()"
            >
                »
            </button>
        </div>
    </div>
</div>

------------------

import { Component, Input, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { BehaviorSubject, Observable, of } from 'rxjs';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-utility-table',
  standalone: true,
  imports: [CommonModule, FormsModule],
  templateUrl: "./utility-table.component.html",
  styleUrls: ['./utility-table.component.css']
})
export class UtilityTableComponent implements OnInit {
  @Input() data: any[] | Observable<any[]> | undefined | null = [];
  @Input() defaultSortColumn: string | null = 'name';
  @Input() defaultSortDirection: 'asc' | 'desc' | '' = 'desc';
  @Input() paginationOptions: number[] = [8, 10, 15, 20];
  @Input() placeholderText: string = 'Search...';
  @Input() isVertical: boolean = false;
  @Input() hideSearchbox: boolean = false;
  @Input() hideViewSelector: boolean = false;
  @Input() hidePaginator: boolean = false;
  @Input() size: "xs" | "sm" | "md" | "lg" = "xs";
  @Input() noRowBorders: boolean = false;
  @Input() noActions: boolean = false;


  itemsPerPage = 0;
  displayedColumns: string[] = [];
  filteredData: any[] = [];
  paginatedData: any[] = [];
  searchTerm: string = '';
  page: number = 1;
  totalPages: number = 1;

  sortColumn: string | null = null;
  sortDirection: 'asc' | 'desc' | '' = '';

  private dataSubject = new BehaviorSubject<any[]>([]);

  ngOnInit() {
    if (this.data !== undefined && this.data !== null) {
      const dataObservable: Observable<any[]> = this.data instanceof Observable ? this.data : of(this.data);
      this.itemsPerPage = this.paginationOptions[0] || 10; // Initialize itemsPerPage safely
      dataObservable.subscribe(data => {
        if (data === null || data === undefined) {
          data = [];
        }
        this.dataSubject.next(data);
        this.initializeTable(data);
        this.applyFilter(); // Initialize filtering and pagination
        if (this.defaultSortColumn) {
          this.sortData(this.defaultSortColumn, false); // Apply default sorting
        }
      });
    }
    if (!this.noActions) {
      this.displayedColumns.push("Actions");
    }
  }
  initializeTable(data: any[]) {
    if (data.length > 0) {
      this.displayedColumns = Object.keys(data[0]);
    }
  }

  applyFilter() {
    this.filteredData = this.dataSubject.getValue().filter(item =>
      Object.values(item).some(
        val => val?.toString().toLowerCase().includes(this.searchTerm.toLowerCase())
      )
    );

    // Calculate total pages based on filtered data
    this.totalPages = Math.ceil(this.filteredData.length / this.itemsPerPage);

    // Ensure current page is within bounds
    if (this.page > this.totalPages) {
      this.page = this.totalPages;
    } else if (this.page < 1) {
      this.page = 1;
    }

    this.paginateData();
  }

  sortData(column: string | null, resetPage: boolean = true) {
    if (!column) return;

    if (this.sortColumn === column) {
      this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc';
    } else {
      this.sortColumn = column;
      this.sortDirection = this.defaultSortDirection; // Use the input default sort direction
    }

    this.filteredData.sort((a, b) => {
      const valueA = a[column] ?? '';
      const valueB = b[column] ?? '';

      const comparison = valueA.toString().localeCompare(valueB.toString(), undefined, { numeric: true });

      return this.sortDirection === 'asc' ? comparison : -comparison;
    });

    if (resetPage) {
      this.page = 1; // Reset to first page after sorting
    }

    this.paginateData();
  }

  paginateData() {
    const startIndex = (this.page - 1) * this.itemsPerPage;
    const endIndex = startIndex + this.itemsPerPage;

    // Ensure endIndex does not exceed filteredData length
    if (endIndex > this.filteredData.length) {
      this.paginatedData = this.filteredData.slice(startIndex);
    } else {
      this.paginatedData = this.filteredData.slice(startIndex, endIndex);
    }
  }

  nextPage() {
    if (this.page < this.totalPages) {
      this.page++;
      this.paginateData();
    }
  }

  previousPage() {
    if (this.page > 1) {
      this.page--;
      this.paginateData();
    }
  }

  onItemsPerPageChange() {
    this.page = 1; // Reset to first page when items per page changes
    this.applyFilter();
  }
}
---------------
function sortArrayByReference(array1, array2) {
    // Check if all elements of array2 are in array1
    for (const element of array2) {
        if (!array1.includes(element)) {
            throw new Error(`Element "${element}" from array2 is not found in array1`);
        }
    }

    // Sorting array1 based on the order defined by array2
    const sortedArray = array1.sort((a, b) => {
        const indexA = array2.indexOf(a);
        const indexB = array2.indexOf(b);

        // Sort elements found in array2 according to their order in array2
        if (indexA !== -1 && indexB !== -1) {
            return indexA - indexB;
        }

        // If one element is found in array2, it should come first
        if (indexA !== -1) {
            return -1;
        }

        if (indexB !== -1) {
            return 1;
        }

        // Maintain original order if neither element is found in array2
        return 0;
    });

    return sortedArray;
}
------------------- typeahead

import { Component, ElementRef, EventEmitter, HostListener, Input, OnChanges, Output, SimpleChanges, ChangeDetectorRef } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { Observable, Subscription } from 'rxjs';
import { map, tap } from 'rxjs/operators';

@Component({
  standalone: true,
  imports: [CommonModule, FormsModule],
  selector: 'app-typeahead',
  template: `
  <div class="relative">
    <!-- Icon and Input Wrapper -->
    <div class="flex items-center border rounded-lg">
        <!-- Search Icon -->
        <span class="px-2 text-gray-500">
            <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 16 16"
                fill="currentColor"
                class="h-4 w-4 opacity-70"
            >
                <path
                    fill-rule="evenodd"
                    d="M9.965 11.026a5 5 0 1 1 1.06-1.06l2.755 2.754a.75.75 0 1 1-1.06 1.06l-2.755-2.754ZM10.5 7a3.5 3.5 0 1 1-7 0 3.5 3.5 0 0 1 7 0Z"
                    clip-rule="evenodd"
                />
            </svg>
        </span>

        <!-- Input Field -->
        <input
            type="text"
            [(ngModel)]="searchText"
            (input)="onInputChange()"
            (focus)="onInputFocus()"
            placeholder="{{placeholderText}}"
            class="w-full p-2 border-none focus:ring-0 focus:outline-none rounded-lg"
        />
    </div>

    <!-- Suggestions List -->
    <ul
  *ngIf="suggestions.length > 0"
  [ngStyle]="{ 'max-height': maxSuggestionListHeight }"
  class="absolute z-10 w-full bg-white border border-gray-300 mt-1 rounded-md shadow-lg overflow-y-auto"
>
  <li
    *ngFor="let suggestion of suggestions; let i = index"
    (click)="selectSuggestion(suggestion)"
    (mouseover)="onMouseOver(i)"
    (mouseout)="onMouseOut()"
    [style.backgroundColor]="i === highlightedIndex ? 'rgb(229 231 235 / var(--tw-bg-opacity))' : 'unset'"
    class="flex items-center justify-between px-4 py-2 cursor-pointer hover:bg-gray-200"
  >

    <!-- Suggestion Text -->
    <span>{{ suggestion }}</span>

    <!-- Check Icon for Selected Item -->
    <svg
      *ngIf="suggestion === selectedSuggestion"
      xmlns="http://www.w3.org/2000/svg"
      class="h-5 w-5 text-green-500"
      fill="none"
      viewBox="0 0 24 24"
      stroke="currentColor"
      stroke-width="2"
    >
      <path
        stroke-linecap="round"
        stroke-linejoin="round"
        d="M5 13l4 4L19 7"
      />
    </svg>
  </li>
</ul>
</div>
  `,
})
export class TypeaheadComponent implements OnChanges {
  @Input() maxSuggestionListHeight: string = '200px'; // Default max height
  @Input() data: Observable<any[]> | any[] = [];
  @Input() placeholderText: string='Search'; // Property placeholderText for the place holder text on the typeahead
  @Input() propertyName: string = ''; // Property name for filtering items
  @Input() sortDirection: 'asc' | 'dsc' | null = null; // Sorting direction, 'asc' for ascending, 'dsc' for descending
  @Input() defaultValue: any = null; // Default value to be selected

  @Output() selectionChange = new EventEmitter<any>(); // Emit selected object

  searchText = '';
  suggestions: any[] = [];
  selectedSuggestion: any | null = null;
  allSuggestions: any[] = [];
  fullData: any[] = []; // To store the full dataset
  private dataSubscription: Subscription | null = null;

  highlightedIndex: number = -1; // Index of highlighted suggestion for arrow navigation
  showSuggestions = false; // Flag to control when to show suggestions

  constructor(private eRef: ElementRef, private cdr: ChangeDetectorRef) { }

  ngOnChanges(changes: SimpleChanges) {
    if (changes['data']) {
      this.loadSuggestions();
    }
    if (changes['propertyName']) {
      try {
        this.validatePropertyName();
        this.loadSuggestions();
      } catch (error: any) {
        console.error(error.message);
      }
    }
    if (changes['defaultValue']) {
      this.setDefaultValue();
    }
  }

  validatePropertyName() {
    if (!this.propertyName) {
      throw new Error('The property name cannot be empty.');
    }

    const sampleObject = this.fullData[0];
    if (!sampleObject) {
      throw new Error('Data is empty. Ensure that your data is loaded before validating property name.');
    }

    const value = this.getNestedProperty(sampleObject, this.propertyName);

    if (Array.isArray(value)) {
      if (value.length === 0 || !this.isPrimitive(value[0])) {
        throw new Error(`The property name "${this.propertyName}" does not point to an array of primitive values.`);
      }
    } else if (!this.isPrimitive(value)) {
      throw new Error(`The property name "${this.propertyName}" does not point to a primitive value.`);
    }
  }

  isPrimitive(value: any): boolean {
    return ['string', 'number', 'boolean'].includes(typeof value);
  }

  loadSuggestions() {
    if (this.data instanceof Observable) {
      if (this.dataSubscription) {
        this.dataSubscription.unsubscribe();
      }
      this.dataSubscription = this.data.pipe(
        tap(items => {
          this.fullData = items;
          this.setDefaultValue(); // Check default value after data is loaded
        }),
        map(items => this.extractSuggestions(items))
      ).subscribe(suggestions => {
        this.allSuggestions = suggestions;
        if (this.showSuggestions) {
          this.suggestions = [...this.allSuggestions];
        }
      });
    } else {
      this.fullData = this.data;
      this.allSuggestions = this.extractSuggestions(this.data);
      if (this.showSuggestions) {
        this.suggestions = [...this.allSuggestions];
      }
      this.setDefaultValue(); // Check default value after data is loaded
    }
  }

  extractSuggestions(items: any[]): any[] {
    if (!this.propertyName) {
      return [];
    }

    const allSuggestions = items
      .map(item => this.getNestedProperty(item, this.propertyName))
      .flat()
      .filter(value => value != null && value !== '')
      .map(value => (typeof value === 'string' ? value.trim() : value));

    let sortedSuggestions = Array.from(new Set(allSuggestions));

    // Apply sorting if sortDirection is provided
    if (this.sortDirection === 'asc') {
      sortedSuggestions.sort((a, b) => (a > b ? 1 : -1));
    } else if (this.sortDirection === 'dsc') {
      sortedSuggestions.sort((a, b) => (a < b ? 1 : -1));
    }

    return sortedSuggestions;
  }

  getNestedProperty(obj: any, propertyPath: string): any {
    const properties = propertyPath.split('.');
    let value = obj;
    for (const prop of properties) {
      if (Array.isArray(value)) {
        value = value.find(item => item && item.hasOwnProperty(prop));
        if (!value) {
          return [];
        }
        value = value[prop];
      } else if (value && value.hasOwnProperty(prop)) {
        value = value[prop];
      } else {
        return [];
      }
    }
    return Array.isArray(value) ? value : [value];
  }

  onInputChange() {
    this.showSuggestions = true;
    const value = this.searchText.toLowerCase();
    this.suggestions = this.allSuggestions.filter(item =>
      (typeof item === 'string' ? item.toLowerCase() : item.toString()).includes(value)
    );
    this.highlightedIndex = -1; // Reset highlighted index on input change
  }

  onInputFocus() {
    this.showSuggestions = true;
    this.suggestions = [...this.allSuggestions];
  }

  selectSuggestion(suggestion: any) {
    this.searchText = typeof suggestion === 'string' ? suggestion : suggestion.toString();
    this.selectedSuggestion = suggestion;
    this.suggestions = [];
    this.showSuggestions = false;

    const selectedObject = this.fullData.find(item =>
      this.getNestedProperty(item, this.propertyName).includes(suggestion)
    );

    if (selectedObject) {
      this.selectionChange.emit(selectedObject);
    }
  }

  // Handle arrow down key to navigate through suggestions
  @HostListener('document:keydown.arrowdown', ['$event'])
  handleArrowDown(event: KeyboardEvent) {
    event.preventDefault(); // Prevent default page scrolling
    if (this.suggestions.length > 0) {
      this.highlightedIndex = (this.highlightedIndex + 1) % this.suggestions.length;
      this.cdr.detectChanges(); // Manually trigger change detection
    }
  }

  // Handle arrow up key to navigate through suggestions
  @HostListener('document:keydown.arrowup', ['$event'])
  handleArrowUp(event: KeyboardEvent) {
    event.preventDefault(); // Prevent default page scrolling
    if (this.suggestions.length > 0) {
      this.highlightedIndex = (this.highlightedIndex - 1 + this.suggestions.length) % this.suggestions.length;
      this.cdr.detectChanges(); // Manually trigger change detection
    }
  }

  // Handle enter key to select the highlighted suggestion
  @HostListener('document:keydown.enter', ['$event'])
  handleEnter(event: KeyboardEvent) {
    if (this.highlightedIndex >= 0 && this.highlightedIndex < this.suggestions.length) {
      this.selectSuggestion(this.suggestions[this.highlightedIndex]);
    }
  }

  @HostListener('document:keydown.escape', ['$event'])
  handleEscape(event: KeyboardEvent) {
    this.suggestions = [];
    this.showSuggestions = false;
  }

  @HostListener('document:click', ['$event'])
  handleClickOutside(event: Event) {
    if (!this.eRef.nativeElement.contains(event.target)) {
      this.suggestions = [];
      this.showSuggestions = false;
    }
  }

  // Handle Alt + Down Arrow to show suggestions
  @HostListener('document:keydown', ['$event'])
  handleAltDown(event: KeyboardEvent) {
    if (event.altKey && event.key === 'ArrowDown') {
      event.preventDefault(); // Prevent default action
      this.showSuggestions = true;
      this.suggestions = [...this.allSuggestions];
      this.highlightedIndex = -1; // Reset highlighted index when showing suggestions
    }
  }

  private setDefaultValue() {
    if (this.defaultValue) {
      const isValidDefault = this.fullData.some(item =>
        this.getNestedProperty(item, this.propertyName).includes(this.defaultValue)
      );

      if (!isValidDefault) {
        throw new Error('The default value provided is not found in the data.');
      }

      this.searchText = typeof this.defaultValue === 'string' ? this.defaultValue : this.defaultValue.toString();
      this.selectedSuggestion = this.defaultValue;
      this.suggestions = [];
      this.showSuggestions = false;

      const selectedObject = this.fullData.find(item =>
        this.getNestedProperty(item, this.propertyName).includes(this.defaultValue)
      );

      if (selectedObject) {
        this.selectionChange.emit(selectedObject);
      }
    }
  }
  onMouseOver(index: number) {
    this.highlightedIndex = index;
  }

  onMouseOut() {
    this.highlightedIndex = -1;
  }
}


-------------
SELECT 
    OBJECT_SCHEMA_NAME(o.object_id) AS [Schema], 
    OBJECT_NAME(o.object_id) AS [ObjectName], 
    o.type_desc AS [ObjectType]
FROM 
    sys.objects o
JOIN 
    sys.sql_modules m ON o.object_id = m.object_id
WHERE 
    m.definition LIKE '%wintoolsapi%'
ORDER BY 
    [Schema], [ObjectName];


------------






import {
  Component,
  Input,
  Output,
  EventEmitter,
  OnInit,
  HostListener,
} from '@angular/core';
import { CommonModule } from '@angular/common';

export type Theme =
  | 'light'
  | 'dark'
  | 'cupcake'
  | 'bumblebee'
  | 'emerald'
  | 'corporate'
  | 'synthwave'
  | 'retro'
  | 'cyberpunk'
  | 'valentine'
  | 'halloween'
  | 'garden'
  | 'forest'
  | 'aqua'
  | 'lofi'
  | 'pastel'
  | 'fantasy'
  | 'wireframe'
  | 'black'
  | 'luxury'
  | 'dracula'
  | 'cmyk'
  | 'autumn'
  | 'business';

@Component({
  selector: 'app-theme-selector',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div class="dropdown mb-72">
      <!-- Dropdown Toggle Button -->
      <div
        tabindex="0"
        role="button"
        aria-label="Select theme"
        class="btn m-1"
        (click)="toggleDropdown()"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          height="24px"
          viewBox="0 -960 960 960"
          width="24px"
        >
          <path
            d="m247-904 57-56 343 343q23 23 23 57t-23 57L457-313q-23 23-57 23t-57-23L153-503q-23-23-23-57t23-57l190-191-96-96Zm153 153L209-560h382L400-751Zm360 471q-33 0-56.5-23.5T680-360q0-21 12.5-45t27.5-45q9-12 19-25t21-25q11 12 21 25t19 25q15 21 27.5 45t12.5 45q0 33-23.5 56.5T760-280ZM80 0v-160h800V0H80Z"
          />
        </svg>
      </div>

      <!-- Dropdown Menu -->
      <ul
        *ngIf="isDropdownOpen"
        tabindex="0"
        class="dropdown-content bg-base-300 rounded-box z-[1] w-52 p-2 shadow-2xl"
      >
        <li *ngFor="let theme of themes">
          <input
            type="radio"
            name="theme-dropdown"
            class="theme-controller btn btn-sm btn-block btn-ghost justify-start"
            [attr.aria-label]="theme"
            [value]="theme"
            [checked]="theme === currentTheme"
            (click)="applyTheme(theme)"
          />
        </li>
      </ul>
    </div>
  `,
  styles: [],
})
export class ThemeSelectorComponent implements OnInit {
  @Input() initialTheme: Theme = 'light';
  @Output() themeChange: EventEmitter<Theme> = new EventEmitter<Theme>();

  themes: Theme[] = [
    'light',
    'dark',
    'cupcake',
    'bumblebee',
    'emerald',
    'corporate',
    'synthwave',
    'retro',
    'cyberpunk',
    'valentine',
    'halloween',
    'garden',
    'forest',
    'aqua',
    'lofi',
    'pastel',
    'fantasy',
    'wireframe',
    'black',
    'luxury',
    'dracula',
    'cmyk',
    'autumn',
    'business',
  ];
  currentTheme: Theme = this.initialTheme;
  isDropdownOpen: boolean = false;

  ngOnInit() {
    if (!this.themes.includes(this.initialTheme)) {
      throw new Error(
        `Invalid initial theme: "${
          this.initialTheme
        }". It must be one of the predefined themes: ${this.themes.join(', ')}.`
      );
    }
    this.applyTheme(this.initialTheme);
  }

  toggleDropdown() {
    this.isDropdownOpen = !this.isDropdownOpen;
  }

  applyTheme(theme: Theme) {
    document.documentElement.setAttribute('data-theme', theme);
    this.currentTheme = theme;
    this.themeChange.emit(this.currentTheme);
    this.isDropdownOpen = false;
  }

  @HostListener('document:keydown.escape', ['$event'])
  handleEscape(event: KeyboardEvent) {
    if (this.isDropdownOpen) {
      this.isDropdownOpen = false;
      event.preventDefault();
    }
  }
}
