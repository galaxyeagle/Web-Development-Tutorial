The webpage (an RSC) will be having an interactive `<DashboardContent>` client component.

Let's see how to create a filter bar with Shadcn components. Note that a readymade filter component is not provided by the shadcn ui library.

Let's make a filter bar with 4 filters: minAge, maxAge, minIncome and minHeight.

const [filters, setFilters] = useState({
  minAge: "",
  maxAge: "",
  minIncome: "",
  minHeight: "",
});
