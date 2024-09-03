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
