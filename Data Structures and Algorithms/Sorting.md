#### Bubble Sort
O(n) best case, O(n^2) worst case
This is easy to remember, think of bubbles bubbling up some water, every bubble is two adjacent elements swapped.
You start at the start of the array, you swap adjacent elements that are out of order. You keep doing this until all elements are in order.

```cpp
void bubbleSort(vector<int>& arr) {
    int n = arr.size();
    // Loop through the array for bubble sort passes.
    for (int i = 0; i < n - 1; ++i) {
        // Inner loop to compare and swap elements.
        for (int j = 0; j < n - i - 1; ++j) {
            // Compare and swap if elements are in the wrong order.
            if (arr[j] > arr[j + 1]) {
                swap(arr[j], arr[j + 1]);
            }
        }
    }
}
```

#### Merge Sort
Merge sort is a divide-and-conquer sorting algorithm. Basically divide the data set into smaller and smaller sub-arrays until it's easy to sort, then merge the sorted subarrays back into a larger sorted array.

```cpp
void merge(vector<int>& arr, int left, int mid, int right, vector<int>& tempArr){
    int start1 = left;
    int start2 = mid + 1;
    int n1 = mid - left + 1;
    int n2 = right - mid;

    for(int i = 0; i < n1; i++){
        tempArr[start1 + i] = arr[start1 + i];
    }
    for(int i = 0; i < n2; i++){
        tempArr[start2 + i] = arr[start2 + i];
    }

    int i = 0, j = 0, k = left;
    while(i < n1 && j < n2) {
        if(tempArr[start1 + i] <= tempArr[start2 + j]) {
            arr[k] = tempArr[start1 + i];
            i++;
        }
        else {
            arr[k] = tempArr[start2 + j];
            j++;
        }
        k++;
    }

    while(i < n1) {
        arr[k] = tempArr[start1 + i];
        i++;
        k++;
    }
    while(j < n2){
        arr[k] = tempArr[start2 + j];
        j++;
        k++;
    }
}

void mergeSort(vector<int>& arr, int left, int right, vector<int>& tempArr){
    if (left >= right) {
        return;
    }
    int mid = (left + right) / 2;
    mergeSort(arr, left, mid, tempArr);
    mergeSort(arr, mid + 1, right, tempArr);
    merge(arr, left, mid, right, tempArr);
}
```