# Prompt tạo React Hook UI Filter cho Laravel API

## Context
Tôi cần tạo một React Hook UI Filter linh hoạt để làm việc với Laravel API backend có system filter phức tạp. System filter này được built trên pattern Laravel Repository-Service với BaseService chứa filter logic chuẩn.

## Filter Pattern của Laravel Backend

### 1. Filter Types trong BaseService:
- **fieldSearchs**: Array fields cho keyword search (OR conditions với LIKE)
- **simpleFilter**: Equal filters cho exact match values  
- **complexFilter**: Advanced operators (gt, gte, lt, lte, eq, between, in)
- **dateFilter**: Date-specific filters với operators (gt, gte, lt, lte, eq, between)

### 2. Query Parameters Format:
```
# Basic
?keyword=search_term&perpage=15&page=1&sort=id,desc

# Simple Filters  
?publish=1&status=ACTIVE&vehicle_catalogue_id=3

# Complex Filters
?id[gt]=10&id[lte]=50&id[between]=10,50
?manufacture_year[gte]=2020&load_capacity[lt]=1000

# Date Filters
?created_at[eq]=2024-01-15
?created_at[gte]=2024-01-01&created_at[lte]=2024-01-31
?created_at[between]=2024-01-01,2024-01-31
?created_at_from=2024-01-01&created_at_to=2024-01-31

# Relations & Type
?with=product_catalogues,users&type=all
```

### 3. Real Examples từ các Service:

**TripService:**
- fieldSearchs: `['code', 'receiver_name', 'receiver_phone']`
- simpleFilter: `['status', 'receiver_name', 'receiver_phone']`
- complexFilter: `['id']`
- dateFilter: `['created_at', 'updated_at']`

**VehicleService:**
- fieldSearchs: `['registration_number', 'management_code', 'brand', 'model_code']`
- simpleFilter: `['publish', 'commercial_use', 'modification', 'tachograph', 'camera', 'vehicle_catalogue_id']`
- complexFilter: `['id', 'manufacture_year', 'load_capacity']`
- dateFilter: `['created_at', 'updated_at', 'registration_date']`

**WarehouseService:**
- fieldSearchs: `['code', 'name']`
- simpleFilter: `['location_id', 'publish']`
- complexFilter: `['id']`
- dateFilter: `['created_at', 'updated_at']`

## Requirements cho React Hook

### 1. Core Features:
- **Dynamic filter configuration** dựa trên filter types của từng service
- **Multiple filter types support**: keyword, simple, complex, date
- **URL state synchronization** với query parameters
- **Debounced search** cho performance
- **Filter validation** và error handling
- **Reset filters** functionality
- **Export filter state** cho persistence

### 2. Hook Interface:
```typescript
interface FilterConfig {
  fieldSearchs?: string[];
  simpleFilter?: string[];
  complexFilter?: string[];
  dateFilter?: string[];
  sort?: [string, 'asc' | 'desc'];
  perpage?: number;
}

interface FilterState {
  keyword?: string;
  simple: Record<string, any>;
  complex: Record<string, Record<string, any>>;
  date: Record<string, Record<string, any>>;
  sort: [string, 'asc' | 'desc'];
  perpage: number;
  page: number;
}

const useApiFilter = (config: FilterConfig) => {
  // Return object with state và methods
}
```

### 3. UI Components cần hỗ trợ:

**Basic Filters:**
- Search input với debounce
- Select dropdowns cho simple filters
- Number inputs cho perpage
- Sort controls

**Advanced Filters:**
- Range inputs cho complex filters (gt, gte, lt, lte, between)
- Date pickers cho date filters
- Multi-select cho 'in' operators
- Boolean toggles cho true/false values

**Filter Layout:**
- Collapsible filter panels
- Active filters display với remove buttons
- Quick filter presets
- Clear all filters button

### 4. Advanced Features:
- **Filter history** để undo/redo
- **Saved filter presets** cho users
- **Real-time filter suggestions** dựa trên data
- **Filter combination logic** (AND/OR)
- **Responsive design** cho mobile
- **Accessibility support** (ARIA labels, keyboard navigation)

### 5. Performance Considerations:
- Debounce cho search inputs (300ms)
- Memoization cho expensive calculations
- Lazy loading cho filter options
- Batch API calls when possible
- Cache filter results

### 6. Integration với Laravel:
- Transform filter state thành Laravel query parameters format
- Handle Laravel pagination response
- Support Laravel validation errors
- Work với Laravel API resources

## Deliverables Required:

1. **useApiFilter Hook** - Core filter logic
2. **FilterProvider Context** - State management 
3. **FilterComponents Library**:
   - SearchInput
   - SimpleFilterSelect
   - ComplexFilterRange  
   - DateFilterPicker
   - SortControl
   - FilterPanel
   - ActiveFiltersTags
4. **TypeScript Interfaces** cho type safety
5. **Documentation** với usage examples
6. **Storybook stories** cho components
7. **Unit tests** cho hook logic

## Example Usage:
```tsx
const ProductFilter = () => {
  const filter = useApiFilter({
    fieldSearchs: ['name', 'code'],
    simpleFilter: ['publish', 'brand_id', 'product_catalogue_id'],
    complexFilter: ['id', 'price', 'price_discount'],
    dateFilter: ['created_at', 'updated_at'],
    sort: ['id', 'desc'],
    perpage: 15
  });

  return (
    <FilterPanel>
      <SearchInput {...filter.keyword} />
      <SimpleFilterSelect 
        field="publish" 
        options={[{value: 1, label: 'Active'}, {value: 0, label: 'Inactive'}]}
        {...filter.simple}
      />
      <ComplexFilterRange 
        field="price" 
        operators={['gte', 'lte']}
        {...filter.complex}
      />
      <DateFilterPicker 
        field="created_at" 
        {...filter.date}
      />
    </FilterPanel>
  );
};
```

Tạo một solution complete, production-ready với modern React patterns (hooks, context, TypeScript) và best practices cho performance và accessibility.
