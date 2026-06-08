# ANGULAR-LEARNING.md — Angular 21 Quick Reference

> Aligned to the sprint scoring rubric. Read this before you write any code.

---

## 1. Module Structure (40 pts — Code Quality)

Always create a **feature module** for your page. Never put everything in `AppModule`.

```typescript
// features/my-feature/my-feature.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule } from '@angular/forms';
import { MyFeatureRoutingModule } from './my-feature-routing.module';
import { MyPageComponent } from './my-page/my-page.component';
import { MyDataService } from './my-data.service';

@NgModule({
  declarations: [MyPageComponent],
  imports: [CommonModule, ReactiveFormsModule, MyFeatureRoutingModule],
  providers: [MyDataService]
})
export class MyFeatureModule {}
```

Register the feature module lazily in `AppRoutingModule`:

```typescript
{ path: 'my-feature', loadChildren: () => import('./features/my-feature/my-feature.module').then(m => m.MyFeatureModule) }
```

---

## 2. Services (40 pts — Code Quality)

All data and business logic lives in a service — not in the component.

```typescript
// features/my-feature/my-data.service.ts
import { Injectable } from '@angular/core';

@Injectable()
export class MyDataService {
  private data = [ /* hardcoded mock */ ];

  getAll() { return this.data; }
  getById(id: number) { return this.data.find(d => d.id === id); }
}
```

Inject it into the component — never instantiate with `new`.

---

## 3. Reactive Forms (25 pts — Functionality)

**Always use Reactive Forms. Never use template-driven forms (ngModel).**

```typescript
// In your component
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

export class MyPageComponent {
  form: FormGroup;

  constructor(private fb: FormBuilder) {
    this.form = this.fb.group({
      name: ['', [Validators.required, Validators.minLength(3)]],
      email: ['', [Validators.required, Validators.email]],
    });
  }

  onSubmit() {
    if (this.form.valid) {
      console.log(this.form.value);
    }
  }
}
```

```html
<!-- In your template -->
<form [formGroup]="form" (ngSubmit)="onSubmit()">
  <input formControlName="name" />
  <div *ngIf="form.get('name')?.invalid && form.get('name')?.touched">
    Name is required (min 3 chars)
  </div>
  <button type="submit" [disabled]="form.invalid">Submit</button>
</form>
```

### Custom Validator

```typescript
// shared/validators/password-match.validator.ts
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

export function passwordMatch(): ValidatorFn {
  return (group: AbstractControl): ValidationErrors | null => {
    const pw = group.get('password')?.value;
    const confirm = group.get('confirmPassword')?.value;
    return pw === confirm ? null : { passwordMismatch: true };
  };
}
```

---

## 4. Component Design (40 pts — Code Quality)

### Smart vs Dumb components

- **Smart (container)** — injects service, holds state, handles routing
- **Dumb (presentational)** — receives `@Input()`, emits `@Output()`, no service injection

```typescript
// Dumb component example
@Component({ selector: 'app-user-card', ... })
export class UserCardComponent {
  @Input() user!: User;
  @Output() edit = new EventEmitter<User>();
}
```

### No logic in templates

Bad ❌
```html
<div>{{ user.firstName + ' ' + user.lastName }}</div>
<div *ngIf="items.filter(i => i.active).length > 0">...</div>
```

Good ✅ — compute in component class:
```typescript
get fullName() { return `${this.user.firstName} ${this.user.lastName}`; }
get hasActiveItems() { return this.items.some(i => i.active); }
```

---

## 5. Routing (25 pts — Functionality)

```typescript
// features/my-feature/my-feature-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { MyPageComponent } from './my-page/my-page.component';
import { MyDetailComponent } from './my-detail/my-detail.component';

const routes: Routes = [
  { path: '', component: MyPageComponent },
  { path: ':id', component: MyDetailComponent },
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class MyFeatureRoutingModule {}
```

Navigate programmatically:
```typescript
constructor(private router: Router) {}
goToDetail(id: number) { this.router.navigate(['/my-feature', id]); }
```

---

## 6. Naming Conventions (40 pts — Code Quality)

| Thing | Convention | Example |
|-------|-----------|---------|
| Component class | PascalCase + `Component` | `LoginComponent` |
| Component file | kebab-case `.component.ts` | `login.component.ts` |
| Service class | PascalCase + `Service` | `AuthService` |
| Module class | PascalCase + `Module` | `AuthModule` |
| Variable/property | camelCase | `isLoading`, `formGroup` |
| Template ref var | camelCase with # | `#myInput` |

---

## 7. Visual / Responsive Tips (35 pts — Visual Accuracy)

- Use your UI library's grid system for layout, not raw CSS floats
- Form labels above inputs (not placeholder text as labels)
- Error messages in red, below the field, only when `touched && invalid`
- Buttons: primary action on the right, cancel/back on the left
- Test at 1280px and 768px widths

---

## Checklist Before PR

```
[ ] Feature module created and lazy-loaded
[ ] Service holds all data/logic — nothing in component templates
[ ] Reactive Forms used throughout — no ngModel
[ ] All required fields have validators
[ ] Validation errors show on touch, not on page load
[ ] Submit button disabled when form invalid
[ ] No complex expressions in HTML templates
[ ] Components named per Angular style guide
[ ] ng build --configuration production completes without errors
[ ] README.md updated with UI library, packages, how to run
```
