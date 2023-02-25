## koa+TS框架
* * *
1. 安装依赖
	- koa框架和ts依赖
		-  `npm insatll koa -S`
		-  `npm insatll @types/koa -S`
	-  *支持POST请求依赖*
		-  `npm install koa-body -S` 
	- *支持响应数据 对象转json格式依赖*
		- `npm install koa-json -S`
		- `npm install @types/koa-json -S`
	- *路由器依赖*
		- `npm install koa-router -S`
		- `npm install @types/koa-router -S`
	- *token依赖*
		- `npm insatll jsonwebtoken -S`
		- `npm insatll @types/jsonwebtoken -S`
	- *javascript 实用工具库*
		- `npm install @types/lodash -S`
	- *日志依赖*
		- `npm install log4js -S`
	- *mysql数据库访问依赖*
		- `npm install mysql -S`
		- `npm install @types/mysql -S`
	- *装饰器元数据*
		- `npm install reflect-metadata -S`
	- *ORM映射工具依赖*
		- `npm install sequelize -S`
		- `npm install sequelize-typescript -S`
	- *自动检测文件变化后自动重启依赖*
		- `npm install nodemon -S`
	- *typescript+ts-node依赖*
		- `npm install typescript -S`
		- `npm install ts-node -S`	
* * *
2. 配置tsconfig.json
```
	{
	  "compilerOptions": {
		"target": "ES2021",
		"useDefineForClassFields": true,
		"module": "CommonJS",
		"moduleResolution": "node",
		"allowJs": true,
		"declaration": false,
		"strict": true,
		"sourceMap": true,
		"resolveJsonModule": true,
		"isolatedModules": true,
		"esModuleInterop": true,
		"lib": ["esnext", "dom"],
		"typeRoots": ["node_modules/@types"],
		"types": ["vite/client", "node"],
		"skipLibCheck": true,
		"baseUrl": ".",
		"paths": {
		  "@/*": ["src/*"]
		}
	  },
	  "include": ["src/**/*.ts", "src/**/*.d.ts"],
	  "exclude": ["node_modules"]
	}

```
* * *
3. 配置启动脚本·「热部署」
```
	"scripts":{
		"dev":"nodemon --watch src/ -e ts --exec ts-node ./src/app.ts"
	}
```

* * *
4. 常规app.ts文件编码()
```
	import Koa from 'koa'
	import allRouterLoader from './common/AllRouterLoader'

	const app = new Koa()
	allRouterLoader.init(app)

```
* * *
5. 配置自动加载路由
	- `import path from 'path'`
	- `import fs from 'fs'`
		- `process.cwd()` 是执行目录
		- `__dirname` 是被执行目录
		- `path.join(process.cwd(),'/src/router')`full目录
		
		
```
	import path from 'path'
	import Koa from 'koa'
	import fs from 'fs'
	import Router from 'koa-router'
	import koaBody from 'koa-body'
	import json from 'koa-json'

	class AllRouterLoader {
	  app!: Koa
	  static allRouterLoader: AllRouterLoader = new AllRouterLoader()

	  init(app: Koa) {
		this.app = app
		const rootRouter = this.loadAllRouterWrapper()
		this.app.use(rootRouter.routes())
		//4. 监听方法
		this.listen()
	  }
	  //1.加载所有路由文件数组
	  getFiles(dir: string) {
		return fs.readdirSync(dir)
	  }
	  //2.加载所有路由文件绝对路径数组
	  getAbsoluteFilePaths() {
		const dir = path.join(process.cwd(), '/src/router')
		const allFiles = this.getFiles(dir)
		const allFullFilePaths: string[] = []
		for (let file of allFiles) {
		  const fullFilePath = dir + '/' + file
		  allFullFilePaths.push(fullFilePath)
		}
		return allFullFilePaths
	  }
	  //3.加载所有一级路由到二级路由中
	  loadAllRouterWrapper() {
		//3.0获取一级路由
		const rootRouter = this.getRootRouter()
		//3.1获取绝对路径数组
		const allFullFilePaths = this.getAbsoluteFilePaths()
		//3.2 加载所有二级路由到一级路由
		this.loadAllRouter(allFullFilePaths, rootRouter)
		return rootRouter
	  }

	  //3.0 获取一级路由
	  getRootRouter() {
		const rootRouter = new Router()
		//为所有路由添加前缀/shucheng
		rootRouter.prefix('/shucheng')
		this.app.use(json())
		this.app.use(koaBody())
		return rootRouter
	  }

	  //自定义守卫（判断是否导入的是路由模块）
	  isRouter(data: any): data is Router {
		return data instanceof Router
	  }

	  loadAllRouter(allFullFilePaths: string[], rootRouter: Router) {
		for (let fullFilePath of allFullFilePaths) {
		  const module = require(fullFilePath)
		  if (this.isRouter(module)) {
			rootRouter.use(module.routes(), module.allowedMethods())
		  }
		}
	  }
	  listen() {
		this.app.listen(8001)
		console.log('server running on port 8001')
	  }
	}

	export default AllRouterLoader.allRouterLoader
```
* * *
6. 全局通用异常 common/globalExce.ts
```
	import Koa, { Context } from 'koa'
	import { success, fail } from './ResResult'

	const globalException = async (ctx: Context, next: Koa.Next) => {
	  console.log('进入到通用异常')
	  try {
		await next()
	  } catch (err: any) {
		const error = err as { message: string }
		ctx.body = fail(`服务器错误${error.message}`)
	  }
	}
	export default globalException

```
* * *
7. 响应处理 common/ResResult.ts
```
	enum Code {
  SUCESS = 200,
  SERVERERROR = 500,
	}
	class ResRsult {
	  static success(data: any = undefined, msg: any = '') {
		const code: Code = Code.SUCESS
		return { data, msg, code }
	  }
	  static fail(msg: any = '') {
		const code: Code = Code.SERVERERROR
		return { undefined, msg, code }
	  }
	}
	export let { success, fail } = ResRsult

```
* * *
8. 