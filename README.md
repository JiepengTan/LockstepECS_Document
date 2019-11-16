# **ReadME**
[Chinese Version][1]
## **1.Setup**
### **1.ClientMode**

1. Open the scene

![Screen Shot 2019-11-13 at 4.48.20 PM.png](https://upload-images.jianshu.io/upload_images/11593954-fb2066c0652e5cd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. Make sure has turn ClientMode on 

![Screen Shot 2019-11-13 at 4.49.50 PM.png](https://upload-images.jianshu.io/upload_images/11593954-5dafa8d3640934b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. Run game

  - A  D :control move direction
  - Space :release skill

### **2.Networking Mode**

- 1.Copy "Assets/LockstepECS/DataAndTools.zip"  to "Assets/../DataAndTools.zip "

![Screen Shot 2019-11-13 at 4.52.02 PM.png](https://upload-images.jianshu.io/upload_images/11593954-557b9e55517c2209.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![Screen Shot 2019-11-13 at 4.52.35 PM.png](https://upload-images.jianshu.io/upload_images/11593954-e7bfe217f459cf3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 2。Decompress 

![Screen Shot 2019-11-13 at 4.54.05 PM.png](https://upload-images.jianshu.io/upload_images/11593954-5bff88745d4fa63a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 3.Open **Game.sln**

![Screen Shot 2019-11-13 at 4.59.36 PM.png](https://upload-images.jianshu.io/upload_images/11593954-b4990f7daa51223e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 4.Build and Run **Game.sln**

![[图片上传中...(Screen Shot 2019-11-13 at 4.50.45 PM.png-75d531-1573782602414-0)]
](https://upload-images.jianshu.io/upload_images/11593954-33601348642f30af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 5.Make sure you had turn **ClientMode** off

![Screen Shot 2019-11-13 at 4.50.45 PM.png](https://upload-images.jianshu.io/upload_images/11593954-0dbb32d16f646c97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 6.Build 

![Screen Shot 2019-11-13 at 5.03.04 PM.png](https://upload-images.jianshu.io/upload_images/11593954-c97d439635944ffc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 7.Run Client

![Screen Shot 2019-11-13 at 5.07.33 PM.png](https://upload-images.jianshu.io/upload_images/11593954-7f73394ad639eadf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 8.Now it simple Lockstep Mode

![Screen Shot 2019-11-13 at 5.09.51 PM.png](https://upload-images.jianshu.io/upload_images/11593954-dbc45db61ba001d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


（In order to test Networking mode,I make the shark spin around a point, you can modify the code in below picture ）
![Screen Shot 2019-11-15 at 10.04.02 AM.png](https://upload-images.jianshu.io/upload_images/11593954-30209b83a9618d66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## **2. Develop with LockstepECS**

**By convention, You should not call the  generated method which start with "_" ，eg:_DoDestroyEntity**
### 1. Directory
![Screen Shot 2019-11-15 at 10.14.28 AM.png](https://upload-images.jianshu.io/upload_images/11593954-512d44c85f835292.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 - 1.Game.Model:
   - Here is MVC's **Model and Controler** code（Logic Layer）

 - 2.Game.View:
  - Here is MVC's **View** code（View Layer），Depend on Logic Layer

 - 3.Tools.UnsafeECS.ECSDefine.Game:
   - You  should declare Entity  Component  System Global state and Events in this dll, And you must make sure it can be build alone,（Can not depend on (Game.Model or Game.View)）
  

### 3. How to use Tools.UnsafeECS.ECSDefine
#### 1. Interfaces
```
   // Copyright 2019 谭杰鹏. All Rights Reserved //https://github.com/JiepengTan 
    /// should declare a Method like:
    /// public void Execute(.....)
    public interface ISystem { }
    /// should declare a Method like:
    /// public void Execute(int index, .....)
    public interface ISystemWithIdx { }
    /// should declare a Method like:
    /// public void Execute(Entity* ptr, .....)
    public interface ISystemWithEntity { }

    /// this system would always be scheduled in main thread 
    /// should declare a Method like:
    /// public void Execute(.....)
    public interface IPureSystem : ISystem { }

    /// this system would always be scheduled in main thread 
    /// should declare a Method like:
    /// public void Execute(Entity* ptr, .....)
    public interface IPureSystemWithEntity : IPureSystem, ISystemWithEntity { }
    
    /// If macro UNING_UNITY_BURST_JOB was defined
    /// this system will be complied by Unity's Burst compiler and scheduled by Unity's JobSystem,Otherwise it will be scheduled call in MainThread
    /// should declare a Method like:
    ///  public void Execute(.....)
    public interface IJobSystem : ISystem { }

    /// If macro UNING_UNITY_BURST_JOB was defined
    /// this system will be complied by Unity's Burst compiler and scheduled by Unity's JobSystem,Otherwise it will be scheduled call in MainThread
    /// should declare a Method like:
    /// public void Execute(Entity* ptr, .....)
    public interface IJobSystemWithEntity : IJobSystem, ISystemWithEntity { }

    /// If macro UNING_UNITY_BURST_JOB was defined
    /// this system will be complied by Unity's Burst compiler and scheduled by Unity's JobSystem,Otherwise it will be scheduled call in MainThread
    /// should declare a Method like:
    /// public void Execute(int index, .....)
    public interface IJobForEachSystem : IJobSystem { }
    /// If macro UNING_UNITY_BURST_JOB was defined
    /// this system will be complied by Unity's Burst compiler and scheduled by Unity's JobSystem,Otherwise it will be scheduled call in MainThread
    /// should declare a Method like:
    /// public void Execute(Entity* ptr,int index, .....)
    public interface IJobForEachSystemWithEntity : IJobForEachSystem, ISystemWithEntity { }

    /// If macro UNING_UNITY_BURST_JOB was defined
    /// this system will be complied by Unity's Burst compiler and scheduled by Unity's JobSystem,Otherwise it will be scheduled call in MainThread
    /// should declare as if it was inherited from : Unity.Collections.IJobNativeMultiHashMapMergedSharedKeyIndices
    public interface IJobHashMapSystem : ISystem { }

    
    /// Component in game
    public interface IGameComponent : IComponent { }
    /// Service using in game
    public interface IGameService{}
    
    /// Game status which need read from config
    public interface IGameConfigService{}
    /// Game status which can not be modified in game
    public interface IGameConstStateService{}
    /// Game status can changed during game 
    public interface IGameStateService{}
    
    
    /// Component
    public interface IComponent { }
    /// Entity
    public interface IEntity { }
    /// Game status
    public interface IServiceState { }
    /// Game Events
    public interface IEvent{}
    /// CollisionEvent define (TODO)
    public interface ICollisionEvent { }

    /// Create a game object to bind Entity
    /// to view the Entity's status or Attach some effect to the gameObject
    /// reference to :CodeGen_EntityView.cs
    public interface IBindViewEntity{}
    /// synchronize Unsafe Entity's Position and Rotation to Unity Entity
    /// reference to :CodeGen_UpdateViewStateSystem.cs
    public interface IUpdateViewEntity{}
```

#### 2. Attributes 
- 1.InitEntityCountAttribute
   Here you can define the init Entity count，use for optimize , similar to the  "capacity" of List<T> 
``` 
    [InitEntityCount(1000)]
    public partial class BoidCell : IEntity{
        public CellData Cell;
    }
``` 

#### 3. Componet 

1. All type can use in Component define
//
```   
   bool
   float  //double is not supported 
   byte
   sbyte
   short
   ushort
   int
   uint
   long
   ulong
   Vector2 
   Vector3 
   Quaternion 
   Vector2Int 
   Vctor3Int
   EntityRef
   Entity 
```

2. Warning!!，You should only declare property in  "Public" ，And should make sure the component is an "IGameComponent"
```
    public partial class BoidState : IGameComponent {
        public float SinkTimer;
        public bool IsDied;
        public bool IsScored;
        public int KillerIdx;
    }
```
3. Component can inherite from other component
-  Component define 
```
    public partial class TestCompBase1 : IGameComponent {
        public int Count;
    }
    public partial class TestCompChild2 : TestCompBase1 {
        public float Radius;
    }
```
- Generated codes
```
   [StructLayoutAttribute(LayoutKind.Sequential)]
    [System.Serializable]
    public unsafe partial struct TestCompBase1 {
        public int Count; 
        public override Int32 GetHashCode() {
            unchecked {
                var hash = 7;
                hash = hash * 37 +Count.GetHashCode();  
                return hash;
            }
        }
    }
    [StructLayoutAttribute(LayoutKind.Sequential)]
    [System.Serializable]
    public unsafe partial struct TestCompChild2 {
        public LFloat Radius;
        public int Count; 
        public override Int32 GetHashCode() {
            unchecked {
                var hash = 7;
                hash = hash * 37 +Radius.GetHashCode();
                hash = hash * 37 +Count.GetHashCode();  
                return hash;
            }
        }
    }
```

#### 4. Entity 

1. Warning!!，You should only declare property in  "Public" ，And should make sure the component is an "IEntity"
2. Entity can inherite from other Entity
- Define

```
    [InitEntityCount(20)]
    public partial class TestEntity1 : IEntity{
        public CellData Cell;
    }

    [InitEntityCount(100)]
    public partial class TestEntity2 : TestEntity1{
        public SpawnData Spawn;
    }
```

- Generated Codes

```
    [StructLayoutAttribute(LayoutKind.Sequential)]
    [System.Serializable]
    public unsafe partial struct TestEntity1 :IEntity {
        public const Int32 INIT_COUNT = 20;
        internal Entity _entity;

        // Fields
        public CellData Cell; 
        // BuildIn properties
        public EntityRef EntityRef =>_entity._ref;
        public int EntityIndex =>_entity._ref._index;
        public EEntityType EntityType => (EEntityType)(_entity._ref._type);
        public bool IsActive =>_entity._active;
    }
    [StructLayoutAttribute(LayoutKind.Sequential)]
    [System.Serializable]
    public unsafe partial struct TestEntity2 :IEntity {
        public const Int32 INIT_COUNT = 100;
        internal Entity _entity;

        // Fields
        public SpawnData Spawn;
        public CellData Cell; 
        // BuildIn properties
        public EntityRef EntityRef =>_entity._ref;
        public int EntityIndex =>_entity._ref._index;
        public EEntityType EntityType => (EEntityType)(_entity._ref._type);
        public bool IsActive =>_entity._active;
    }
```

3. IUpdateViewEntity
If you wan to synchronize the Position and Rotation of Entity from UnsafeECS  to Unity, you should let the entity inherite from IUpdateViewEntity, reference to "CodeGen_UpdateViewStateSystem.cs"
```
    [InitEntityCount(2000)]
    public partial class Boid: IEntity,IUpdateViewEntity{
        public Transform3D Transform;
        public Prefab Prefab;
        public BoidState State;
        public BoidTag Tag;
    }
```

4. IBindViewEntity
If you want your entity has a gameObject proxy to Attach some VFX or some unity Component, You shouldlet the entity inherite from "IBindViewEntity"，reference to  CodeGen_EntityView.cs

```
    [InitEntityCount(2)]
    public partial class BoidTarget: IEntity,IBindViewEntity {
        public Transform3D Transform;
        public Prefab Prefab;
        public TargetMoveInfo MoveInfo;
        public BoidTargetTag Tag;
    }
```

![Screen Shot 2019-11-15 at 1.01.13 PM.png](https://upload-images.jianshu.io/upload_images/11593954-d604b2d2a84b8c03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 5. Systems
1. **Overview**

- System Define 
    - IJobSystem
    - IPureSystem
- System Implement
    - GameJobSystem
    - GameExecuteSystem
- If the system was declared as an **IJobSystem** and macro **UNING_UNITY_BURST_JOB** was defined this system will be complied by Unity's Burst compiler and scheduled by Unity's JobSystem,Otherwise it will be scheduled call in **MainThread**，so that kind of System should not modify global states (can read , should not write)
- If the system was declared as an **IPureSystem**, then it would be scheduled in main thread ,so this kind of system can deal some Task which need modify global state eg: DestroyEntity

```
 public abstract unsafe class BaseExecuteSystem : BaseSystem, IExecuteSystem {
        public void Execute(IContext context){
            if (BeforeSchedule()) {  DoSchedule(context);  }
            AfterSchedule(isSucc);
        }
        protected virtual bool BeforeSchedule(){ return true;}
        protected virtual void DoSchedule(IContext context){  context.Schedule(this);  }
        protected virtual void AfterSchedule(bool isSucc){ }
    }
 public partial class GameExecuteSystem :BaseExecuteSystem{   }
```

- **BeforeSchedule**  this function would call before Execute，you can override this function to setup some state used in function "Execute",if this function's return value was "False" ,this System would not be scheduled in this frame

- **AfterSchedule**  this function would call after Execute, you can override it to do some clean task after "Execute" function was scheduled

2. **PureSystem** 

this kind of system was inherited from IPureSystem eg:

- IPureSystem
- IPureSystemWithEntity

that kind of system  would be scheduled in main thread ,so this kind of system can deal some Task which need modify global state eg: CreateEnttiy,DestroyEntity,Modify _gameStateService's property
you can reference to the system "SpawnSystem" in demo.

- System Define 

```
    public class SpawnSystem : IPureSystemWithEntity {
        public SpawnData SpawnData;
        public AssetData AssetData;
    }
```

- System Implement

``` 
 public unsafe partial class SpawnSystem : GameExecuteSystem {
        public void Execute(Entity* entity, ref SpawnData spawnData, ref AssetData assetData){
            var count = spawnData.Count;
            var center = spawnData.Position;
            var radius = spawnData.Radius;
            var spawnPositions = new NativeArray<LVector3>(count, Allocator.Temp,
                NativeArrayOptions.UninitializedMemory);
            GeneratePoints.RandomPointsInUnitCube(spawnPositions);
            var pointPtr = spawnPositions.GetPointer(0);
            var context = _context;
            for (int i = 0; i < count; ++i, ++pointPtr) {
                var boidPtr = context.PostCmdCreateBoid();
                boidPtr->Transform.Position = center + (*pointPtr * radius);
                boidPtr->Transform.Forward = *pointPtr;
                boidPtr->Transform.Scale = 1;
                boidPtr->Prefab.AssetId = assetData.AssetId;
            }
            spawnPositions.Dispose();
            context.PostCmdDestroyEntity(entity);
        }
    }
```

3. **JobSystem**

this kind of system was inherited from IPureSystem eg:

- IJobSystem
- IJobSystemWithEntity
- IJobForEachSystem
- IJobForEachSystemWithEntity
- IJobHashMapSystem

If macro UNING_UNITY_BURST_JOB was defined this system will be complied by Unity's Burst compiler and scheduled by Unity's JobSystem,Otherwise it will be scheduled call in MainThread
**WARNING:should not modify global states (can read , should not write)**

- System Define

```    
    public class SinkSystem : IJobSystem {
        public Transform3D Transform;
        public BoidState BoidState;
    }
```

- System Implement

```
 public unsafe partial class SinkSystem : GameJobSystem {
        public unsafe partial struct JobDefine {
            [ReadOnly] public LVector3 SinkOffset;
            [ReadOnly] public LFloat DeltaTime;

            public void Execute(ref Transform3D transform3D, ref BoidState boidState){
                if (!boidState.IsDied) return;
                boidState.SinkTimer -= DeltaTime;
                transform3D.Position += SinkOffset;
            }
        }

        protected override bool BeforeSchedule(){
            //assign jobData info
            JobData.DeltaTime = _globalStateService.DeltaTime;
            JobData.SinkOffset = new LVector3(0,_gameConfigService.BoidSettting.SinkSpd * JobData.DeltaTime,0) ;
            return true;
        }
    }
```

#### 6. Status & Config

- You should declare all property that you need to change or maintain in game at "GameStateService" 
framework would backup or rollback it if need.

```
    public partial class GameStateService : IServiceState,IGameStateService {
        // states
        public bool IsPlaying;
        public bool IsGameOver;
        public byte LocalEntityId; 

        // volatile states
        public int CurEnemyCount;
        public int CurScore;
        public float CurScale;
        
    }
```

- if you want to config some variable, you can declare they in class **GameConfigService** ，framework would generate a ScriptObject to config (The class using in ECDefine  only need a declare( doesn't need all  definition)，to make sure it can be compile，reference to Tools.UnsafeECS.ECDefine.Game/Src/Unsafe/Status.cs)

```
    public partial class GameConfigService : IServiceState,IGameConfigService {
        public string RelPath;
        public string RecorderFilePath;
        public string DumpStrPath;

        public int InitScale;
        public int MaxPlayerCount;

        public CollisionConfig CollisionConfig;
        public Msg_G2C_GameStartInfo ClientModeInfo;
        public List<ConfigTargetInfo> TargetInfos;
        public List<ConfigSpawnInfo> SpawnInfos;
        public List<ConfigObstacleInfo> ObstacleInfos;
        public ConfigBoidSharedData BoidSettting;
    }
```

#### 7. Events

```
    public class OnSkillFire: IEvent{
        public SkillData SkillData;
    }
    public class OnSkillDone: IEvent{
        public SkillData SkillData;
    }
```

 framework would generated some code to implement they in IGameEventService  
you can implement IGameEventService, or reRaise these event;
 
```
    public unsafe partial interface IGameEventService : IService{
        void OnSkillFire(Entity* ptr
                ,ref SkillData SkillData             
            );
        void OnSkillDone(Entity* ptr
                ,ref SkillData SkillData             
            );    
    } 
```

you can call function RaiseEventXxxx  in GameExecuteSystem
eg:

```
    public unsafe partial class SkillSystem : GameExecuteSystem {
        private LFloat _deltaTime;
        public void Execute(Entity* ptr,ref SkillData skillData){
            //skillData.IsFiring = skillMonoData.IsFiring;
            if (skillData.IsNeedFire) {
                if (skillData.CdTimer <= 0 && !skillData.IsFiring) {
                    skillData.CdTimer = skillData.CD;
                    skillData.IsFiring = true;
                    skillData.DurationTimer = skillData.Duration;
                    RaiseEventOnSkillFire(ptr,ref skillData);
                }
            }

            skillData.CdTimer -= _deltaTime;
            if (skillData.IsFiring) {
                skillData.DurationTimer -= _deltaTime;
                if (skillData.DurationTimer <= 0) {
                    skillData.IsFiring = false;
                    RaiseEventOnSkillDone(ptr,ref skillData);
                }
            }

            skillData.IsNeedFire = false;
        }
      ...
```


 [1]: https://github.com/JiepengTan/LockstepECS_Document/blob/master/README_CN.md