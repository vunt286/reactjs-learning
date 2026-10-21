# Hướng dẫn cho AI: Xây dựng project ReactJS chuẩn

## 1. Stack mặc định
- Build tool: **Vite** (không dùng Create React App vì đã ngừng bảo trì)
- Ngôn ngữ: **TypeScript** (strict mode)
- React 18+/19, function component + hooks (không dùng class component)
- Routing: **React Router**
- Server state: **TanStack Query**; client state: **Zustand** (chỉ dùng khi cần)
- Styling: **Tailwind CSS** (hoặc CSS Modules)
- Lint/format: **ESLint + Prettier**
- Test: **Vitest + React Testing Library**
- Package manager: **pnpm** (hoặc npm nếu dự án yêu cầu)

## 2. Khởi tạo project
```bash
pnpm create vite@latest my-app -- --template react-ts
cd my-app
pnpm install
pnpm add react-router-dom @tanstack/react-query zustand axios
pnpm add -D tailwindcss @tailwindcss/vite prettier eslint-config-prettier \
  vitest jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

## 3. Cấu trúc thư mục (feature-based)
```
src/
├── app/            # App.tsx, router, providers (QueryClient, Router)
├── assets/         # ảnh, font
├── components/     # component dùng chung (Button, Modal, ...)
├── features/       # mỗi tính năng 1 thư mục
│   └── auth/
│       ├── components/
│       ├── hooks/
│       ├── api.ts
│       ├── types.ts
│       └── index.ts   # public API của feature
├── hooks/          # custom hook dùng chung
├── lib/            # axios instance, utils, constants
├── pages/          # component cấp trang, map với route
├── store/          # zustand stores
├── types/          # type dùng chung
├── main.tsx
└── index.css
```
Quy tắc: feature không import trực tiếp nội bộ của feature khác, chỉ qua `index.ts`.

## 4. Quy ước code
- Component: PascalCase, 1 component / file, `UserCard.tsx`
- Hook: bắt đầu bằng `use`, `useUserList.ts`
- Props khai báo bằng `type`/`interface`, không dùng `any`
- Không dùng index làm `key` khi list có thể thay đổi
- Tách logic ra custom hook, component chỉ lo render
- Dùng `useMemo`/`useCallback` khi thật sự cần, không lạm dụng
- Không gọi API trực tiếp trong component; đặt trong `api.ts` + TanStack Query
- Luôn xử lý 3 trạng thái: loading, error, empty
- Biến môi trường: tiền tố `VITE_`, khai báo trong `.env.example`, không commit `.env`
- Dùng path alias `@/` trỏ tới `src/`

## 5. Ví dụ mẫu
```tsx
// src/features/users/components/UserList.tsx
import { useUsers } from '../hooks/useUsers';

export function UserList() {
  const { data, isLoading, error } = useUsers();

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Có lỗi xảy ra</p>;
  if (!data?.length) return <p>Không có dữ liệu</p>;

  return (
    <ul>
      {data.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

## 6. Scripts bắt buộc trong package.json
```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "preview": "vite preview",
    "lint": "eslint .",
    "format": "prettier --write .",
    "test": "vitest"
  }
}
```

## 7. Quy trình AI phải tuân theo
1. Đọc cấu trúc hiện có trước khi tạo file mới.
2. Tuân thủ stack và cấu trúc ở trên, không tự thêm thư viện nếu chưa cần.
3. Sau khi sửa code, chạy: `pnpm lint && pnpm test && pnpm build`.
4. Viết test cho logic quan trọng và component có tương tác.
5. Commit theo Conventional Commits: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`.

## 8. Không được làm
- Không dùng `any`, không tắt rule ESLint tùy tiện
- Không mutate state trực tiếp
- Không hardcode URL/API key trong code
- Không commit `node_modules`, `.env`, `dist`
- Không tạo component quá 200 dòng, hãy tách nhỏ
