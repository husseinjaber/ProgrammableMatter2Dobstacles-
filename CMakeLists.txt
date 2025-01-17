cmake_minimum_required(VERSION 3.15)
project(VisibleSim)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-long-long -Wpedantic")

include_directories(simulatorCore/src)

if(UNIX AND NOT APPLE)
    set(LINUX TRUE)
endif()

IF (WIN32)
    message(STATUS ">>> Win32")
    add_compile_definitions(USE_FREEGLUT)
    set(GLUT_INTERFACE_DIRS  ../glutInterface)
    set(GLUT_INTERFACE_LIB_DIRS  ../glutInterface/cmake-build-debug)
    include_directories(${GLUT_INTERFACE_DIRS})

    add_compile_definitions(ROOT_DIR="F:/1-Enseignement/Master IOT 2/VisibleSim/VisibleSim-Tutorials")
    add_compile_definitions(DEBUG_GRAPHICS)

    IF (MSVC)
        message(STATUS "+Visual studio")
        set(GLUT_DIR F:/freeglut-3.0.0)
        set(CMAKE_PREFIX_PATH FREEGLUT)

        set(FREEGLUT_INCLUDE_DIRS F:/freeglut-3.0.0/include)
        set(FREEGLUT_LIBRARY_DIRS F:/freeglut-3.0.0/cmake-build-debug/lib)
        set(FREEGLUT_LIBRARIES libfreeglut_static.a)

        find_package(OPENGL REQUIRED)

        include_directories(${FREEGLUT_INCLUDE_DIRS} ${OPENGL_INCLUDE_DIRS} ${GLUT_INTERFACE_DIRS})
        link_directories(${FREEGLUT_LIBRARY_DIRS} ${OPENGL_LIBRARY_DIRS} ${GLUT_INTERFACE_LIB_DIRS})
        add_definitions(${FREEGLUT_DEFINITIONS} ${OPENGL_DEFINITIONS})
        add_executable(${PROJECT_NAME} ${PROJECT_FILES})
        target_link_libraries(${PROJECT_NAME} ${FREEGLUT_LIBRARIES} ${GLUT_LIBRARIES} ${OPENGL_LIBRARIES} glutInterface.lib)
    ENDIF(MSVC)

    IF(MINGW)
        message(STATUS "+MinGW")

        set(FREEGLUT_INCLUDE_DIRS F:/freeglut/include)
        set(FREEGLUT_LIBRARY_DIRS F:/freeglut/lib)
        add_compile_definitions(GLEW_STATIC)
        set(GLEW_INCLUDE_DIRS F:/glew-2.1.0/include)
        set(GLEW_LIBRARY_DIRS F:/glew-2.1.0/lib)
        set(muparser_INCLUDE_DIRS simulatorCore/src/deps/muParser)
        set(muparser_LIBRARY_DIRS F:/muparser-master/cmake-build-debug)

        set(ROBOTS_LIBRARY_DIRS ./cmake-build-debug)

        link_directories(${ROBOTS_LIBRARY_DIRS} ${FREEGLUT_LIBRARY_DIRS} ${GLEW_LIBRARY_DIRS} ${muparser_LIBRARY_DIRS})
        include_directories(${FREEGLUT_INCLUDE_DIRS} ${GLUT_LIBRARIES} ${GLEW_INCLUDE_DIRS} ${muparser_INCLUDE_DIRS})
    ENDIF(MINGW)
ENDIF (WIN32)

IF(LINUX)
    message(STATUS ">>> Linux")
    add_compile_definitions(USE_FREEGLUT)

    SET(GLUT_INCLUDE_DIR /usr/include)
    SET(GLUT_glut_LIBRARY /usr/lib/x86_64-linux-gnu/libglut.so)
    set(LIBSMARTBLOCKS_DIRS  ./cmake-build-debug)
    SET(LIBSMARTBLOCKS libSmartBlocks.so)
# GLUT
    #########################################################
    # FIND GLUT
    #########################################################
    find_package(GLUT REQUIRED)
    #include_directories(${GLUT_INCLUDE_DIRS})
    link_directories(${GLUT_LIBRARY_DIRS})
    add_definitions(${GLUT_DEFINITIONS})
    if(NOT GLUT_FOUND)
        message(ERROR " GLUT not found!")
    endif(NOT GLUT_FOUND)

    # OpenGL
    find_package(OpenGL REQUIRED)

    include_directories( ${OPENGL_INCLUDE_DIRS} ${GLUT_INCLUDE_DIRS} ${LIBSMARTBLOCKS_DIRS})

ENDIF(LINUX)

IF (APPLE)
    message(STATUS ">>> APPLE")

    find_package(OpenGL REQUIRED)
    find_package(GLUT REQUIRED)
    include_directories(${OPENGL_INCLUDE_DIRS}  ${GLUT_INCLUDE_DIRS} )
ENDIF(APPLE)

add_definitions(${FREEGLUT_DEFINITIONS} ${OPENGL_DEFINITIONS})
set(COMMON_SRC_FILES
        simulatorCore/src/base/blockCode.cpp
        simulatorCore/src/base/blockCode.h
        simulatorCore/src/base/buildingBlock.cpp
        simulatorCore/src/base/buildingBlock.h
        simulatorCore/src/base/glBlock.cpp
        simulatorCore/src/base/glBlock.h
        simulatorCore/src/base/simulator.cpp
        simulatorCore/src/base/simulator.h
        simulatorCore/src/base/world.cpp
        simulatorCore/src/base/world.h
        simulatorCore/src/clock/clock.cpp
        simulatorCore/src/clock/clock.h
        simulatorCore/src/clock/clockNoise.cpp
        simulatorCore/src/clock/clockNoise.h
        simulatorCore/src/clock/qclock.cpp
        simulatorCore/src/clock/qclock.h
        simulatorCore/src/comm/network.cpp
        simulatorCore/src/comm/network.h
        simulatorCore/src/comm/rate.cpp
        simulatorCore/src/comm/rate.h
        simulatorCore/src/csg/csg.cpp
        simulatorCore/src/csg/csg.h
        simulatorCore/src/csg/csgParser.cpp
        simulatorCore/src/csg/csgParser.h
        simulatorCore/src/deps/Eigen/src/Cholesky/LDLT.h
        simulatorCore/src/deps/Eigen/src/Cholesky/LLT.h
        simulatorCore/src/deps/Eigen/src/Cholesky/LLT_LAPACKE.h
        simulatorCore/src/deps/Eigen/src/CholmodSupport/CholmodSupport.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AltiVec/Complex.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AltiVec/MathFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AltiVec/PacketMath.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AVX/Complex.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AVX/MathFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AVX/PacketMath.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AVX/TypeCasting.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AVX512/MathFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/arch/AVX512/PacketMath.h
        simulatorCore/src/deps/Eigen/src/Core/arch/CUDA/Complex.h
        simulatorCore/src/deps/Eigen/src/Core/arch/CUDA/Half.h
        simulatorCore/src/deps/Eigen/src/Core/arch/CUDA/MathFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/arch/CUDA/PacketMath.h
        simulatorCore/src/deps/Eigen/src/Core/arch/CUDA/PacketMathHalf.h
        simulatorCore/src/deps/Eigen/src/Core/arch/CUDA/TypeCasting.h
        simulatorCore/src/deps/Eigen/src/Core/arch/Default/ConjHelper.h
        simulatorCore/src/deps/Eigen/src/Core/arch/Default/Settings.h
        simulatorCore/src/deps/Eigen/src/Core/arch/NEON/Complex.h
        simulatorCore/src/deps/Eigen/src/Core/arch/NEON/MathFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/arch/NEON/PacketMath.h
        simulatorCore/src/deps/Eigen/src/Core/arch/SSE/Complex.h
        simulatorCore/src/deps/Eigen/src/Core/arch/SSE/MathFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/arch/SSE/PacketMath.h
        simulatorCore/src/deps/Eigen/src/Core/arch/SSE/TypeCasting.h
        simulatorCore/src/deps/Eigen/src/Core/arch/ZVector/Complex.h
        simulatorCore/src/deps/Eigen/src/Core/arch/ZVector/MathFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/arch/ZVector/PacketMath.h
        simulatorCore/src/deps/Eigen/src/Core/functors/AssignmentFunctors.h
        simulatorCore/src/deps/Eigen/src/Core/functors/BinaryFunctors.h
        simulatorCore/src/deps/Eigen/src/Core/functors/NullaryFunctors.h
        simulatorCore/src/deps/Eigen/src/Core/functors/StlFunctors.h
        simulatorCore/src/deps/Eigen/src/Core/functors/TernaryFunctors.h
        simulatorCore/src/deps/Eigen/src/Core/functors/UnaryFunctors.h
        simulatorCore/src/deps/Eigen/src/Core/products/GeneralBlockPanelKernel.h
        simulatorCore/src/deps/Eigen/src/Core/products/GeneralMatrixMatrix.h
        simulatorCore/src/deps/Eigen/src/Core/products/GeneralMatrixMatrix_BLAS.h
        simulatorCore/src/deps/Eigen/src/Core/products/GeneralMatrixMatrixTriangular.h
        simulatorCore/src/deps/Eigen/src/Core/products/GeneralMatrixMatrixTriangular_BLAS.h
        simulatorCore/src/deps/Eigen/src/Core/products/GeneralMatrixVector.h
        simulatorCore/src/deps/Eigen/src/Core/products/GeneralMatrixVector_BLAS.h
        simulatorCore/src/deps/Eigen/src/Core/products/Parallelizer.h
        simulatorCore/src/deps/Eigen/src/Core/products/SelfadjointMatrixMatrix.h
        simulatorCore/src/deps/Eigen/src/Core/products/SelfadjointMatrixMatrix_BLAS.h
        simulatorCore/src/deps/Eigen/src/Core/products/SelfadjointMatrixVector.h
        simulatorCore/src/deps/Eigen/src/Core/products/SelfadjointMatrixVector_BLAS.h
        simulatorCore/src/deps/Eigen/src/Core/products/SelfadjointProduct.h
        simulatorCore/src/deps/Eigen/src/Core/products/SelfadjointRank2Update.h
        simulatorCore/src/deps/Eigen/src/Core/products/TriangularMatrixMatrix.h
        simulatorCore/src/deps/Eigen/src/Core/products/TriangularMatrixMatrix_BLAS.h
        simulatorCore/src/deps/Eigen/src/Core/products/TriangularMatrixVector.h
        simulatorCore/src/deps/Eigen/src/Core/products/TriangularMatrixVector_BLAS.h
        simulatorCore/src/deps/Eigen/src/Core/products/TriangularSolverMatrix.h
        simulatorCore/src/deps/Eigen/src/Core/products/TriangularSolverMatrix_BLAS.h
        simulatorCore/src/deps/Eigen/src/Core/products/TriangularSolverVector.h
        simulatorCore/src/deps/Eigen/src/Core/util/BlasUtil.h
        simulatorCore/src/deps/Eigen/src/Core/util/Constants.h
        simulatorCore/src/deps/Eigen/src/Core/util/DisableStupidWarnings.h
        simulatorCore/src/deps/Eigen/src/Core/util/ForwardDeclarations.h
        simulatorCore/src/deps/Eigen/src/Core/util/Macros.h
        simulatorCore/src/deps/Eigen/src/Core/util/Memory.h
        simulatorCore/src/deps/Eigen/src/Core/util/Meta.h
        simulatorCore/src/deps/Eigen/src/Core/util/MKL_support.h
        simulatorCore/src/deps/Eigen/src/Core/util/NonMPL2.h
        simulatorCore/src/deps/Eigen/src/Core/util/ReenableStupidWarnings.h
        simulatorCore/src/deps/Eigen/src/Core/util/StaticAssert.h
        simulatorCore/src/deps/Eigen/src/Core/util/XprHelper.h
        simulatorCore/src/deps/Eigen/src/Core/Array.h
        simulatorCore/src/deps/Eigen/src/Core/ArrayBase.h
        simulatorCore/src/deps/Eigen/src/Core/ArrayWrapper.h
        simulatorCore/src/deps/Eigen/src/Core/Assign.h
        simulatorCore/src/deps/Eigen/src/Core/Assign_MKL.h
        simulatorCore/src/deps/Eigen/src/Core/AssignEvaluator.h
        simulatorCore/src/deps/Eigen/src/Core/BandMatrix.h
        simulatorCore/src/deps/Eigen/src/Core/Block.h
        simulatorCore/src/deps/Eigen/src/Core/BooleanRedux.h
        simulatorCore/src/deps/Eigen/src/Core/CommaInitializer.h
        simulatorCore/src/deps/Eigen/src/Core/ConditionEstimator.h
        simulatorCore/src/deps/Eigen/src/Core/CoreEvaluators.h
        simulatorCore/src/deps/Eigen/src/Core/CoreIterators.h
        simulatorCore/src/deps/Eigen/src/Core/CwiseBinaryOp.h
        simulatorCore/src/deps/Eigen/src/Core/CwiseNullaryOp.h
        simulatorCore/src/deps/Eigen/src/Core/CwiseTernaryOp.h
        simulatorCore/src/deps/Eigen/src/Core/CwiseUnaryOp.h
        simulatorCore/src/deps/Eigen/src/Core/CwiseUnaryView.h
        simulatorCore/src/deps/Eigen/src/Core/DenseBase.h
        simulatorCore/src/deps/Eigen/src/Core/DenseCoeffsBase.h
        simulatorCore/src/deps/Eigen/src/Core/DenseStorage.h
        simulatorCore/src/deps/Eigen/src/Core/Diagonal.h
        simulatorCore/src/deps/Eigen/src/Core/DiagonalMatrix.h
        simulatorCore/src/deps/Eigen/src/Core/DiagonalProduct.h
        simulatorCore/src/deps/Eigen/src/Core/Dot.h
        simulatorCore/src/deps/Eigen/src/Core/EigenBase.h
        simulatorCore/src/deps/Eigen/src/Core/ForceAlignedAccess.h
        simulatorCore/src/deps/Eigen/src/Core/Fuzzy.h
        simulatorCore/src/deps/Eigen/src/Core/GeneralProduct.h
        simulatorCore/src/deps/Eigen/src/Core/GenericPacketMath.h
        simulatorCore/src/deps/Eigen/src/Core/GlobalFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/Inverse.h
        simulatorCore/src/deps/Eigen/src/Core/IO.h
        simulatorCore/src/deps/Eigen/src/Core/Map.h
        simulatorCore/src/deps/Eigen/src/Core/MapBase.h
        simulatorCore/src/deps/Eigen/src/Core/MathFunctions.h
        simulatorCore/src/deps/Eigen/src/Core/MathFunctionsImpl.h
        simulatorCore/src/deps/Eigen/src/Core/Matrix.h
        simulatorCore/src/deps/Eigen/src/Core/MatrixBase.h
        simulatorCore/src/deps/Eigen/src/Core/NestByValue.h
        simulatorCore/src/deps/Eigen/src/Core/NoAlias.h
        simulatorCore/src/deps/Eigen/src/Core/NumTraits.h
        simulatorCore/src/deps/Eigen/src/Core/PermutationMatrix.h
        simulatorCore/src/deps/Eigen/src/Core/PlainObjectBase.h
        simulatorCore/src/deps/Eigen/src/Core/Product.h
        simulatorCore/src/deps/Eigen/src/Core/ProductEvaluators.h
        simulatorCore/src/deps/Eigen/src/Core/Random.h
        simulatorCore/src/deps/Eigen/src/Core/Redux.h
        simulatorCore/src/deps/Eigen/src/Core/Ref.h
        simulatorCore/src/deps/Eigen/src/Core/Replicate.h
        simulatorCore/src/deps/Eigen/src/Core/ReturnByValue.h
        simulatorCore/src/deps/Eigen/src/Core/Reverse.h
        simulatorCore/src/deps/Eigen/src/Core/Select.h
        simulatorCore/src/deps/Eigen/src/Core/SelfAdjointView.h
        simulatorCore/src/deps/Eigen/src/Core/SelfCwiseBinaryOp.h
        simulatorCore/src/deps/Eigen/src/Core/Solve.h
        simulatorCore/src/deps/Eigen/src/Core/SolverBase.h
        simulatorCore/src/deps/Eigen/src/Core/SolveTriangular.h
        simulatorCore/src/deps/Eigen/src/Core/StableNorm.h
        simulatorCore/src/deps/Eigen/src/Core/Stride.h
        simulatorCore/src/deps/Eigen/src/Core/Swap.h
        simulatorCore/src/deps/Eigen/src/Core/Transpose.h
        simulatorCore/src/deps/Eigen/src/Core/Transpositions.h
        simulatorCore/src/deps/Eigen/src/Core/TriangularMatrix.h
        simulatorCore/src/deps/Eigen/src/Core/VectorBlock.h
        simulatorCore/src/deps/Eigen/src/Core/VectorwiseOp.h
        simulatorCore/src/deps/Eigen/src/Core/Visitor.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/ComplexEigenSolver.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/ComplexSchur.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/ComplexSchur_LAPACKE.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/EigenSolver.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/GeneralizedEigenSolver.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/GeneralizedSelfAdjointEigenSolver.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/HessenbergDecomposition.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/MatrixBaseEigenvalues.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/RealQZ.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/RealSchur.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/RealSchur_LAPACKE.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/SelfAdjointEigenSolver.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/SelfAdjointEigenSolver_LAPACKE.h
        simulatorCore/src/deps/Eigen/src/Eigenvalues/Tridiagonalization.h
        simulatorCore/src/deps/Eigen/src/Geometry/arch/Geometry_SSE.h
        simulatorCore/src/deps/Eigen/src/Geometry/AlignedBox.h
        simulatorCore/src/deps/Eigen/src/Geometry/AngleAxis.h
        simulatorCore/src/deps/Eigen/src/Geometry/EulerAngles.h
        simulatorCore/src/deps/Eigen/src/Geometry/Homogeneous.h
        simulatorCore/src/deps/Eigen/src/Geometry/Hyperplane.h
        simulatorCore/src/deps/Eigen/src/Geometry/OrthoMethods.h
        simulatorCore/src/deps/Eigen/src/Geometry/ParametrizedLine.h
        simulatorCore/src/deps/Eigen/src/Geometry/Quaternion.h
        simulatorCore/src/deps/Eigen/src/Geometry/Rotation2D.h
        simulatorCore/src/deps/Eigen/src/Geometry/RotationBase.h
        simulatorCore/src/deps/Eigen/src/Geometry/Scaling.h
        simulatorCore/src/deps/Eigen/src/Geometry/Transform.h
        simulatorCore/src/deps/Eigen/src/Geometry/Translation.h
        simulatorCore/src/deps/Eigen/src/Geometry/Umeyama.h
        simulatorCore/src/deps/Eigen/src/Householder/BlockHouseholder.h
        simulatorCore/src/deps/Eigen/src/Householder/Householder.h
        simulatorCore/src/deps/Eigen/src/Householder/HouseholderSequence.h
        simulatorCore/src/deps/Eigen/src/IterativeLinearSolvers/BasicPreconditioners.h
        simulatorCore/src/deps/Eigen/src/IterativeLinearSolvers/BiCGSTAB.h
        simulatorCore/src/deps/Eigen/src/IterativeLinearSolvers/ConjugateGradient.h
        simulatorCore/src/deps/Eigen/src/IterativeLinearSolvers/IncompleteCholesky.h
        simulatorCore/src/deps/Eigen/src/IterativeLinearSolvers/IncompleteLUT.h
        simulatorCore/src/deps/Eigen/src/IterativeLinearSolvers/IterativeSolverBase.h
        simulatorCore/src/deps/Eigen/src/IterativeLinearSolvers/LeastSquareConjugateGradient.h
        simulatorCore/src/deps/Eigen/src/IterativeLinearSolvers/SolveWithGuess.h
        simulatorCore/src/deps/Eigen/src/Jacobi/Jacobi.h
        simulatorCore/src/deps/Eigen/src/LU/arch/Inverse_SSE.h
        simulatorCore/src/deps/Eigen/src/LU/Determinant.h
        simulatorCore/src/deps/Eigen/src/LU/FullPivLU.h
        simulatorCore/src/deps/Eigen/src/LU/InverseImpl.h
        simulatorCore/src/deps/Eigen/src/LU/PartialPivLU.h
        simulatorCore/src/deps/Eigen/src/LU/PartialPivLU_LAPACKE.h
        simulatorCore/src/deps/Eigen/src/MetisSupport/MetisSupport.h
        simulatorCore/src/deps/Eigen/src/misc/blas.h
        simulatorCore/src/deps/Eigen/src/misc/Image.h
        simulatorCore/src/deps/Eigen/src/misc/Kernel.h
        simulatorCore/src/deps/Eigen/src/misc/lapack.h
        simulatorCore/src/deps/Eigen/src/misc/lapacke.h
        simulatorCore/src/deps/Eigen/src/misc/lapacke_mangling.h
        simulatorCore/src/deps/Eigen/src/misc/RealSvd2x2.h
        simulatorCore/src/deps/Eigen/src/OrderingMethods/Amd.h
        simulatorCore/src/deps/Eigen/src/OrderingMethods/Eigen_Colamd.h
        simulatorCore/src/deps/Eigen/src/OrderingMethods/Ordering.h
        simulatorCore/src/deps/Eigen/src/PardisoSupport/PardisoSupport.h
        simulatorCore/src/deps/Eigen/src/PaStiXSupport/PaStiXSupport.h
        simulatorCore/src/deps/Eigen/src/plugins/ArrayCwiseBinaryOps.h
        simulatorCore/src/deps/Eigen/src/plugins/ArrayCwiseUnaryOps.h
        simulatorCore/src/deps/Eigen/src/plugins/BlockMethods.h
        simulatorCore/src/deps/Eigen/src/plugins/CommonCwiseBinaryOps.h
        simulatorCore/src/deps/Eigen/src/plugins/CommonCwiseUnaryOps.h
        simulatorCore/src/deps/Eigen/src/plugins/MatrixCwiseBinaryOps.h
        simulatorCore/src/deps/Eigen/src/plugins/MatrixCwiseUnaryOps.h
        simulatorCore/src/deps/Eigen/src/QR/ColPivHouseholderQR.h
        simulatorCore/src/deps/Eigen/src/QR/ColPivHouseholderQR_LAPACKE.h
        simulatorCore/src/deps/Eigen/src/QR/CompleteOrthogonalDecomposition.h
        simulatorCore/src/deps/Eigen/src/QR/FullPivHouseholderQR.h
        simulatorCore/src/deps/Eigen/src/QR/HouseholderQR.h
        simulatorCore/src/deps/Eigen/src/QR/HouseholderQR_LAPACKE.h
        simulatorCore/src/deps/Eigen/src/SparseCholesky/SimplicialCholesky.h
        simulatorCore/src/deps/Eigen/src/SparseCholesky/SimplicialCholesky_impl.h
        simulatorCore/src/deps/Eigen/src/SparseCore/AmbiVector.h
        simulatorCore/src/deps/Eigen/src/SparseCore/CompressedStorage.h
        simulatorCore/src/deps/Eigen/src/SparseCore/ConservativeSparseSparseProduct.h
        simulatorCore/src/deps/Eigen/src/SparseCore/MappedSparseMatrix.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseAssign.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseBlock.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseColEtree.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseCompressedBase.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseCwiseBinaryOp.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseCwiseUnaryOp.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseDenseProduct.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseDiagonalProduct.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseDot.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseFuzzy.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseMap.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseMatrix.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseMatrixBase.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparsePermutation.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseProduct.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseRedux.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseRef.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseSelfAdjointView.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseSolverBase.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseSparseProductWithPruning.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseTranspose.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseTriangularView.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseUtil.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseVector.h
        simulatorCore/src/deps/Eigen/src/SparseCore/SparseView.h
        simulatorCore/src/deps/Eigen/src/SparseCore/TriangularSolver.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_column_bmod.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_column_dfs.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_copy_to_ucol.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_gemm_kernel.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_heap_relax_snode.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_kernel_bmod.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_Memory.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_panel_bmod.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_panel_dfs.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_pivotL.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_pruneL.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_relax_snode.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_Structs.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_SupernodalMatrix.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLU_Utils.h
        simulatorCore/src/deps/Eigen/src/SparseLU/SparseLUImpl.h
        simulatorCore/src/deps/Eigen/src/SparseQR/SparseQR.h
        simulatorCore/src/deps/Eigen/src/SPQRSupport/SuiteSparseQRSupport.h
        simulatorCore/src/deps/Eigen/src/StlSupport/details.h
        simulatorCore/src/deps/Eigen/src/StlSupport/StdDeque.h
        simulatorCore/src/deps/Eigen/src/StlSupport/StdList.h
        simulatorCore/src/deps/Eigen/src/StlSupport/StdVector.h
        simulatorCore/src/deps/Eigen/src/SuperLUSupport/SuperLUSupport.h
        simulatorCore/src/deps/Eigen/src/SVD/BDCSVD.h
        simulatorCore/src/deps/Eigen/src/SVD/JacobiSVD.h
        simulatorCore/src/deps/Eigen/src/SVD/JacobiSVD_LAPACKE.h
        simulatorCore/src/deps/Eigen/src/SVD/SVDBase.h
        simulatorCore/src/deps/Eigen/src/SVD/UpperBidiagonalization.h
        simulatorCore/src/deps/Eigen/src/UmfPackSupport/UmfPackSupport.h
        simulatorCore/src/deps/TinyXML/tinystr.cpp
        simulatorCore/src/deps/TinyXML/tinystr.h
        simulatorCore/src/deps/TinyXML/tinyxml.cpp
        simulatorCore/src/deps/TinyXML/tinyxml.h
        simulatorCore/src/deps/TinyXML/tinyxmlerror.cpp
        simulatorCore/src/deps/TinyXML/tinyxmlparser.cpp
        simulatorCore/src/events/cppScheduler.cpp
        simulatorCore/src/events/cppScheduler.h
        simulatorCore/src/events/events.cpp
        simulatorCore/src/events/events.h
        simulatorCore/src/events/scheduler.cpp
        simulatorCore/src/events/scheduler.h
        simulatorCore/src/events/uniqueEventsId.h
        simulatorCore/src/grid/cell3DPosition.cpp
        simulatorCore/src/grid/cell3DPosition.h
        simulatorCore/src/grid/lattice.cpp
        simulatorCore/src/grid/lattice.h
        simulatorCore/src/grid/target.cpp
        simulatorCore/src/grid/target.h
        simulatorCore/src/gui/camera.cpp
        simulatorCore/src/gui/camera.h
        simulatorCore/src/gui/interface.cpp
        simulatorCore/src/gui/interface.h
        simulatorCore/src/gui/objLoader.cpp
        simulatorCore/src/gui/objLoader.h
        simulatorCore/src/gui/openglViewer.cpp
        simulatorCore/src/gui/openglViewer.h
        simulatorCore/src/gui/shaders.cpp
        simulatorCore/src/gui/shaders.h
        simulatorCore/src/gui/vertexArray.cpp
        simulatorCore/src/gui/vertexArray.h
        simulatorCore/src/math/matrix44.cpp
        simulatorCore/src/math/matrix44.h
        simulatorCore/src/math/vector3D.cpp
        simulatorCore/src/math/vector3D.h
        simulatorCore/src/meld/meldInterpretEvents.cpp
        simulatorCore/src/meld/meldInterpretEvents.h
        simulatorCore/src/meld/meldInterpretMessages.cpp
        simulatorCore/src/meld/meldInterpretMessages.h
        simulatorCore/src/meld/meldInterpretScheduler.cpp
        simulatorCore/src/meld/meldInterpretScheduler.h
        simulatorCore/src/meld/meldInterpretVM.cpp
        simulatorCore/src/meld/meldInterpretVM.h
        simulatorCore/src/motion/teleportationEvents.cpp
        simulatorCore/src/motion/teleportationEvents.h
        simulatorCore/src/motion/translationEvents.cpp
        simulatorCore/src/motion/translationEvents.h
        simulatorCore/src/stats/configStat.cpp
        simulatorCore/src/stats/configStat.h
        simulatorCore/src/stats/statsCollector.cpp
        simulatorCore/src/stats/statsCollector.h
        simulatorCore/src/stats/statsIndividual.cpp
        simulatorCore/src/stats/statsIndividual.h
        simulatorCore/src/utils/color.cpp
        simulatorCore/src/utils/color.h
        simulatorCore/src/utils/commandLine.cpp
        simulatorCore/src/utils/commandLine.h
        simulatorCore/src/utils/configExporter.cpp
        simulatorCore/src/utils/configExporter.h
        simulatorCore/src/utils/exceptions.h
        simulatorCore/src/utils/global.h
        simulatorCore/src/utils/random.cpp
        simulatorCore/src/utils/random.h
        simulatorCore/src/utils/scenario.h
        simulatorCore/src/utils/sema.h
        simulatorCore/src/utils/tDefs.h
        simulatorCore/src/utils/trace.cpp
        simulatorCore/src/utils/trace.h
        simulatorCore/src/utils/utils.cpp
        simulatorCore/src/utils/utils.h
        simulatorCore/src/replay/replayExporter.cpp
        simulatorCore/src/replay/replayExporter.h
        simulatorCore/src/replay/replayTags.h
        )

        add_library(BlinkyBlocks STATIC ${COMMON_SRC_FILES}
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksBlock.cpp
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksBlock.h
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksBlockCode.cpp
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksBlockCode.h
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksEvents.cpp
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksEvents.h
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksGlBlock.cpp
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksGlBlock.h
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksScenario.h
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksSimulator.cpp
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksSimulator.h
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksWorld.cpp
        simulatorCore/src/robots/blinkyBlocks/blinkyBlocksWorld.h
        )

    target_link_libraries(BlinkyBlocks -lOpenGL32 -lglu32 -lglewd -lfreeglut -lpthread -lmuparser)

add_library(Catoms3DBlocks SHARED ${COMMON_SRC_FILES}
        simulatorCore/src/robots/catoms3D/catoms3DBlock.cpp
        simulatorCore/src/robots/catoms3D/catoms3DBlock.h
        simulatorCore/src/robots/catoms3D/catoms3DBlockCode.cpp
        simulatorCore/src/robots/catoms3D/catoms3DBlockCode.h
        simulatorCore/src/robots/catoms3D/catoms3DRotationEvents.cpp
        simulatorCore/src/robots/catoms3D/catoms3DRotationEvents.h
        simulatorCore/src/robots/catoms3D/catoms3DMotionEngine.cpp
        simulatorCore/src/robots/catoms3D/catoms3DMotionEngine.h
        simulatorCore/src/robots/catoms3D/catoms3DMotionRules.cpp
        simulatorCore/src/robots/catoms3D/catoms3DMotionRules.h
        simulatorCore/src/robots/catoms3D/catoms3DGlBlock.cpp
        simulatorCore/src/robots/catoms3D/catoms3DGlBlock.h
        simulatorCore/src/robots/catoms3D/catoms3DSimulator.cpp
        simulatorCore/src/robots/catoms3D/catoms3DSimulator.h
        simulatorCore/src/robots/catoms3D/catoms3DWorld.cpp
        simulatorCore/src/robots/catoms3D/catoms3DWorld.h
        )

target_link_libraries(Catoms3DBlocks ${FREEGLUT_LIBRARIES} ${GLUT_LIBRARIES} ${OPENGL_LIBRARIES})

add_library(SmartBlocks STATIC ${COMMON_SRC_FILES}
        simulatorCore/src/robots/smartBlocks/smartBlocksBlock.cpp
        simulatorCore/src/robots/smartBlocks/smartBlocksBlock.h
        simulatorCore/src/robots/smartBlocks/smartBlocksBlockCode.cpp
        simulatorCore/src/robots/smartBlocks/smartBlocksBlockCode.h
        simulatorCore/src/robots/smartBlocks/smartBlocksGlBlock.cpp
        simulatorCore/src/robots/smartBlocks/smartBlocksGlBlock.h
        simulatorCore/src/robots/smartBlocks/smartBlocksSimulator.cpp
        simulatorCore/src/robots/smartBlocks/smartBlocksSimulator.h
        simulatorCore/src/robots/smartBlocks/smartBlocksWorld.cpp
        simulatorCore/src/robots/smartBlocks/smartBlocksWorld.h
        )
    target_link_libraries(SmartBlocks -lOpenGL32 -lglu32 -lglew32d -lfreeglut -lpthread -lmuparser)

add_library(Hexanodes STATIC ${COMMON_SRC_FILES}
        simulatorCore/src/robots/hexanodes/hexanodesBlock.cpp
        simulatorCore/src/robots/hexanodes/hexanodesBlock.h
        simulatorCore/src/robots/hexanodes/hexanodesBlockCode.cpp
        simulatorCore/src/robots/hexanodes/hexanodesBlockCode.h
        simulatorCore/src/robots/hexanodes/hexanodesGlBlock.cpp
        simulatorCore/src/robots/hexanodes/hexanodesGlBlock.h
        simulatorCore/src/robots/hexanodes/hexanodesMotionEngine.cpp
        simulatorCore/src/robots/hexanodes/hexanodesMotionEngine.h
        simulatorCore/src/robots/hexanodes/hexanodesMotionEvents.cpp
        simulatorCore/src/robots/hexanodes/hexanodesMotionEvents.h
        simulatorCore/src/robots/hexanodes/hexanodesSimulator.cpp
        simulatorCore/src/robots/hexanodes/hexanodesSimulator.h
        simulatorCore/src/robots/hexanodes/hexanodesWorld.cpp
        simulatorCore/src/robots/hexanodes/hexanodesWorld.h
        )
target_link_libraries(Hexanodes -lOpenGL32 -lglu32 -lglew32d -lfreeglut -lpthread -lmuparser)

add_executable(stressTestSB applicationsSrc/stressTestSB/stressTestSB.cpp applicationsSrc/stressTestSB/stressTestSBBlockCode.cpp)
target_link_libraries(stressTestSB -lSmartBlocks -lfreeglut -lmuparser -lOpenGL32 -lglu32 -lglew32d) #
#target_link_libraries(stressTestSB ${FREEGLUT_LIBRARIES} ${GLUT_LIBRARIES} ${OPENGL_LIBRARIES} ${LIBSMARTBLOCKS})

add_executable(myApp applicationsSrc/myApp/myApp.cpp applicationsSrc/myApp/myAppCode.cpp)
target_link_libraries(myApp -lBlinkyBlocks -lfreeglut -lmuparser -lOpenGL32 -lglu32 -lglew32d) #

add_executable(ABCbasic applicationsSrc/ABCbasic/ABCbasic.cpp applicationsSrc/ABCbasic/ABCbasicCode.cpp)
target_link_libraries(ABCbasic -lBlinkyBlocks -lfreeglut -lmuparser -lOpenGL32 -lglu32 -lglew32d) #

add_executable(hexaReconf applicationsSrc/hexaReconf/hexaReconf.cpp applicationsSrc/hexaReconf/hexaReconfCode.cpp)
target_link_libraries(hexaReconf -lHexanodes -lfreeglut -lmuparser -lOpenGL32 -lglu32 -lglew32d) #

#add_executable(simpleVcell applicationsSrc/simpleVcell/simpleVcellCode.cpp applicationsSrc/simpleVcell/simpleVcell.cpp)
#target_link_libraries(simpleVcell -lhexanodes -lfreeglut -lmuparser -lOpenGL32 -lglu32 -lglew32d) #
#target_link_libraries(stressTestSB ${FREEGLUT_LIBRARIES} ${GLUT_LIBRARIES} ${OPENGL_LIBRARIES} ${LIBSMARTBLOCKS})

