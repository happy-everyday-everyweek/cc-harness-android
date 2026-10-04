# Claude Desktop 逆向分析

## 包来源
https://downloads.claude.ai/claude-desktop/apt/stable/pool/main/c/claude-desktop/claude-desktop_2.9939.4_amd64.deb

## Agent 协议模式（全部 build JS）
tool_use: 69
tool_result: 68
tool_choice: 0
permission_mode: 6
allowedTools: 0
disallowedTools: 1
input_schema: 0
mcpServers: 16
content_block_stop: 6

## tool_use 上下文样本
sistant"&&e.parent_tool_use_id==null?L9(e.message,"tool_use","id"):[]}function L9(e,t,n){let r=Z5(e)?e.content:void 0;if(!Array.isArray(r))return[];let i=[];for
stant":return!e.message.content.some((e=>e.type==="tool_use"&&(e.name==="SendUserMessage"||e.name==="PushNotification")));case"system":return"subtype"in e&&(e.s
thinking_delta"?"thinking":o==="input_json_delta"?"tool_use":"text",is_first_message:i.isFirstMessage,is_resume:i.isResume,session_type:n.sessionType,parent_ses
al(),_meta:M(D(),Rn()).optional()}),uqe=j({type:N("tool_use"),name:D(),id:D(),input:M(D(),Rn()),_meta:M(D(),Rn()).optional()}),dqe=j({type:N("resource"),resourc
nal(),_meta:M(D(),Rn()).optional()}),He=j({type:N("tool_use"),name:D(),id:D(),input:M(D(),Rn()),_meta:M(D(),Rn()).optional()}),Ue=j({type:N("resource"),resource
nal(),_meta:M(D(),Rn()).optional()}),_e=j({type:N("tool_use"),name:D(),id:D(),input:M(D(),Rn()),_meta:M(D(),Rn()).optional()}),ve=j({type:N("resource"),resource

## tool_result 上下文样本
="user"&&this.toolResultReleases(e.message))return"tool_result";if(!(this.holdRule===void 0||this.mayWaitUnderHold(e)))return"event"}toolResultReleases(e){let t=th
ools:void 0;return t!=="write"&&t!=="all"?!1:L9(e,"tool_result","tool_use_id").some((e=>t==="all"||this.gatedWriteToolCalls.has(e)))}gateToolCall(e){let t=this.flu
Uploader.enqueue(c,l?{release:"event"}:u?{release:"tool_result"}:d.some((e=>this.awaitedToolCalls.has(e)))?{release:"tool_call"}:this.transcriptRowMayWait(e,s)?voi
turn Array.isArray(t)&&t.some((e=>Z5(e)&&e.type==="tool_result"))}function I9(e){return Z5(e)&&e.type==="assistant"&&e.parent_tool_use_id==null?L9(e.message,"tool_
["message","content"],((e,n)=>{if(Lt(e)&&e.type==="tool_result"&&Array.isArray(e.content)){let r=Gt(e.content,[...n,"content"],((e,n)=>Wt(e,n,t)));return r===e.con
urn typeof e=="object"&&!!e&&"type"in e&&e.type==="tool_result"&&"tool_use_id"in e&&typeof e.tool_use_id=="string"}function Uc(e){if(typeof e=="string")return e;if

## mcpServers 上下文样本
path:'orgPluginSettings as a {"mcpServers": {\u2026}} record',use:'the array form [{"serverName": "\u2026", "tools": [{"toolName": "\u2026", "permission": "\u2026
```\nThe older record form (`{"mcpServers": {"internal-search": {"toolPolicy": {"delete_document": "blocked"}}}}`) is deprecated and accepted only until '+hSe(STe
;if(!e||typeof e!="object"||!("mcpServers"in e)||!e.mcpServers||typeof e.mcpServers!="object")return{filteredConfig:e,invalidServers:t};let n=Object.create(null);
t)?[]:Object.keys(t)}var oXt=["mcpServers","agents"],sXt=262144;function yE(e){if(e.clis&&typeof e.clis=="object"&&!Array.isArray(e.clis)){let t=Object.create(nul
 e of oXt){let n=bXt(r,e);e==="mcpServers"&&(n=yXt(n,t)),n!==void 0&&(i[e]=n,a=!0)}return a?i:null}function yXt(e,t){let n=e=>typeof e=="object"&&!!e&&!Array.isAr
 r of Object.keys(xE)){if(r==="mcpServers"){if(await TXt(e,(0,n.join)(e,".mcp.json")))return!1;let r=t?.mcpServers,i=Array.isArray(r)?r.filter((e=>typeof e=="obje

## permission_mode 上下文样本
urn}P=E5(e.request_id,t);break}case"set_permission_mode":{let t=Wre(e.request.mode),n=t===void 0?{ok:!1,error:Gre}:g?.(t)??{ok:!1,error
Bp())}var Qie=new Set(["set_model","set_permission_mode","interrupt","stop_task","background_tasks","cancel_async_message","set_max_thi
ode-${n.hex32()}`,request:{subtype:"set_permission_mode",mode:t.permissionMode}}}}),t.folders!==void 0&&t.folders.length>0&&r.push({pay
u.name,prompt:u.prompt,permissionMode:u.permission_mode==="auto"||u.permission_mode==="bypassPermissions"?u.permission_mode:void 0,fold
re??"no_window"}`}}let{prompt:w,model:T,permission_mode:E,...D}=u;return{body:{...D,...d!==void 0&&{folders:d},target_device_id:x,...S&
rorDetail:"modes_unknown"};let l=e.body.permission_mode;if(l!==void 0&&l!=="default"&&!c.has(l))return{outcome:"declined_mode_unavailab

## Cowork 工具上下文样本
ome((e=>e.type==="tool_use"&&(e.name==="SendUserMessage"||e.name==="PushNotification")));case"system":return"subtype"in e&&(e.subtype==="thinking
he outcome, then report to the user via SendUserMessage.`;this.dispatchIdleWaiters.has(e.sessionId)||(r.inputStream?this.enqueueMetaNotification(
he outcome, then report to the user via SendUserMessage.`;if(i.inputStream){this.enqueueMetaNotification(i,a);return}if(i.lifecycleState==="initi
ant"&&n.sessionType==="agent"&&n.turnHadSendUserMessage===!1&&e.parent_tool_use_id==null&&Array.isArray(e.message?.content)){for(let r of e.messa
)&&r.name==="SendUserMessage"){n.turnHadSendUserMessage=!0;break}}if(e.type==="assistant"&&e.parent_tool_use_id==null&&Array.isArray(e.message?.c
alls:r.turnToolCallCount??0,...r.turnHadSendUserMessage!==void 0&&{had_send_user_message:r.turnHadSendUserMessage}}),a==="error_max_turns"&&t._M(
stResultUnreachable=!1,r.pendingCycleHadSendUserMessage=r.turnHadSendUserMessage,r.turnHadSendUserMessage=r.sessionType!=="agent"&&void 0,r.turnL
:Y,genAtBuild:se,hasHtmlArtifacts:ee,hasSendUserMessage:Ft,hasWritingDraft:Pt,imagineElicitationEnabled:ut,inputStream:be,localPlugins:Ue,mountDi

## toolPolicy 上下文样本
ames:r.slice(0,20).join(",")})}}let n=f.toolPolicy?m.filter(t=>e.Bq(f.toolPolicy,t.name)!=="blocked"):m,g=a?n.filter(e=>a[`${f.name}:${e.nam
:"respawn_failed"),t.t(P(s))}}if(e.Bq(g.toolPolicy,u)==="blocked")return{content:[{type:"text",text:`Tool '${u}' is not permitted`}],isError
:"Tool policy",id:"uSQ/bLKBIp"}),_Te=j({toolPolicy:mTe.optional()}),vTe=j({mcpServers:M(D(),_Te.catch({})).optional()});function yTe(e){if(A
=>({serverName:e,tools:Object.entries(t.toolPolicy??{}).map((([e,t])=>({toolName:e,permission:t})))}))):e}var bTe=j({toolName:gs(D().min(1),
s(e.mcpServers).flatMap((e=>Ks(e)&&Ks(e.toolPolicy)?[e.toolPolicy]:[])):[],wTe={path:'orgPluginSettings[].tools[].permission: "ask-session"'
cpServers).map((([e,t])=>[e,Ks(t)&&Ks(t.toolPolicy)?{...t,toolPolicy:fTe(t.toolPolicy)}:t])))}}};function TTe(e){return ts(STe,ts(wTe,e))}va

## agents 配置上下文样本
xK(),TK(),OK(),kK=["commands","agents","output-styles","skills","workflows","routines","themes","rules","session-env","uploads","mcp-skill
,q("sessions"),vf],[q("v1"),q("agents"),J],[q("v1"),q("environments"),q("bridge"),J],[q("v1"),q("environments"),J],[q("v1"),q("deployments
lts"),J],[q("workspaces"),J,q("agents"),J],[q("workspaces"),J,q("environments"),J],[q("workspaces"),J,q("deployments"),J],[q("workspaces")
ns"),vf],[q("v1"),q("beta"),q("agents"),J],[q("v1"),q("beta"),q("environments"),J],[q("v1"),q("beta"),q("deployments"),J],[q("v1"),q("beta
keys(t)}var oXt=["mcpServers","agents"],sXt=262144;function yE(e){if(e.clis&&typeof e.clis=="object"&&!Array.isArray(e.clis)){let t=Object
s",commands:"commands",agents:"agents",hooks:"hooks",mcpServers:"."},dXt=new Set([...Object.values(xE).filter((e=>e!==".")),".claude-plugi

## 最大的 20 个 JS 文件
6600826 unpacked/.vite/build/index.chunk-DuaKZOPP.js
1595761 unpacked/.vite/build/index.chunk-B9SZqsi8.js
1251838 unpacked/.vite/build/index.chunk-BEotU2NU.js
1125678 unpacked/.vite/build/index.chunk-vRLpacfH.js
1051034 unpacked/.vite/build/index.pre.js
834824 unpacked/.vite/build/file-index-worker/fileIndexWorker.js
631831 unpacked/.vite/build/index.chunk-CKt-cwRV.js
593839 unpacked/.vite/build/mcp-runtime/directMcpHost.js
541578 unpacked/.vite/build/index.chunk-vKHHEwY1.js
433463 unpacked/.vite/build/heavy-work-worker/dist-CI9lzb1v.js
352646 unpacked/.vite/build/index.chunk-DYbdAm12.js
342326 unpacked/.vite/renderer/quick_window/assets/main-CcJZQl2t.js
342021 unpacked/.vite/build/heavy-work-worker/heavyWorkWorker.js
323193 unpacked/.vite/renderer/about_window/assets/main-rKKlJFrg.js
323182 unpacked/.vite/renderer/buddy_window/assets/main-DaiFXWfK.js
323173 unpacked/.vite/renderer/find_in_page/assets/main-DFc6D1kL.js
304489 unpacked/.vite/build/index.chunk-D6vCltAs.js
288472 unpacked/.vite/build/index.chunk-CD_xAMwd.js
257139 unpacked/.vite/build/index.chunk-B9nHsrmb.js
197031 unpacked/.vite/build/index.chunk-BfINPqEi.js

## 文件总数与总大小
367
63M	unpacked
